# Componet

## 相关

* **CamelContextAware接口**。提供CamelContext识别。
* Aware（意识），将外部Bean等和CamelContext绑定在一起，是一种设计模式。
  * ApplicationContextAware   能获取Application Context调用容器的服务
  * BeanNameAware    提供对BeanName进行操作
  * ApplicationEventPublisherAware   主要用于事件的发布
  * BeanClassLoadAware 相关的类加载器
  * BeanFactoryAware 声明BeanFactory的
* **ComponentAware接口**。使成为Component。

  ```java
  派生类：
  default <T extends Component> T getComponent(Class<T> type) {
        final Component component = getComponent();

        if (component == null) {
            return null;
        }

        if (Component.class.isAssignableFrom(type)) {
            return type.cast(component);
        }

        throw new IllegalArgumentException(
            "Unable to unwrap the Component type (" + component.getClass() + ") to the required type (" + type + ")"
        );
    }
  ```

* **ComponentExtension接口**。提供对组件的扩展，只有一个unwrap方法。

## 定义

```java
/**
 * A component is
 * a factory of {@link Endpoint} objects.
 */
public interface Component extends CamelContextAware {

    Endpoint createEndpoint(String uri) throws Exception;

    boolean useRawUri();

    default Collection<Class<? extends ComponentExtension>> getSupportedExtensions() {
        return Collections.emptyList();
    }

    default <T extends ComponentExtension> Optional<T> getExtension(Class<T> extensionType) {
        return Optional.empty();
    }

    static <T> T trySetComponent(T object, Component component) {
        if (object instanceof ComponentAware) {
            ((ComponentAware) object).setComponent(component);
        }

        return object;
    }
}
```

* 组件是用于创建EndPoint的工厂；
* createEndpoint根据传入的Uri创建一个EndPoint；
* useRawUri，是否使用原始的uri，false，即使用编码的；
* 支持对组件的扩展
* trySetComponent，工厂方法，对某个对象，通过Aware设置Component。

