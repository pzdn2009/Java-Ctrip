# Persist Example

Persist to demonstrate how a **database entry update logic** can be **controlled** by a state machine.

参考官方的例子：[https://docs.spring.io/spring-statemachine/docs/current/reference/htmlsingle/\#statemachine-examples-persist](https://docs.spring.io/spring-statemachine/docs/current/reference/htmlsingle/#statemachine-examples-persist)

## 1. 使用PersistStateMachineHandler

直接使用官方例子的代码：

* 实现拦截器StateMachineInterceptorAdapter
* 实现组合监听器AbstractCompositeListener

## 2. 实现PersistStateChangeListener持久化

持久化的逻辑：

```java
/**
 * 本地持久化监听器
 */
private class LocalPersistStateChangeListener implements PersistStateMachineHandler.PersistStateChangeListener {

    /**
     * 持久化时发生
     *
     * @param state        the state
     * @param message      the message
     * @param transition   the transition
     * @param stateMachine the state machine
     */
    @Override
    public void onPersist(State<String, String> state,
                          Message<String> message,
                          Transition<String, String> transition,
                          StateMachine<String, String> stateMachine) {
        if (message != null && message.getHeaders().containsKey(ORDER_ID)) {
            //需要更新状态
            if (!transition.getSource().equals(transition.getTarget())) {
                //表示转换成功
                transition = true;
            }
        }
    }
}
```

## 3. 接缝

```java
/**
 * 构造器
 *
 * @param handler
 */
public OrderStatusService(PersistStateMachineHandler handler) {
    this.handler = handler;
    this.handler.addPersistStateChangeListener(listener);
}

/**
 * 尝试转换
 *
 */
public ChangeResult tryMakeTransition(String orderId, OrderEvent orderEvent) {

    transition = false;

    //query order.

    if (unionOrderStatusEntity != null) {
        var transitionSuccess = handler.handleEventWithState(MessageBuilder.withPayload(unionOrderEvent.name())
                .setHeader(ORDER_ID, unionOrderStatusEntity).build(), unionOrderStatusEntity.getStatus().name());
        if (transitionSuccess) {
            if (transition) {
                return ChangeResult.REQUEST;
            } else {
                return ChangeResult.IDEM;
            }
        }
    }

    return ChangeResult.ERROR;
}

//other logic ...
```

