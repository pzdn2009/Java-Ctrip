# Timer Component

URI: timer:name\[?options\]

* name：名称。which is created and shared across endpoints.

选项：

* period：

## TimerEndpoint

用于定义Timer EndPoint.

* 实现接口：DefaultEndpoint
* createProducer，没有实现。
* createConsumer，创建一个TimerConsumer。
* getComponent，创建TimerComponent。

## TimerConsumer

* 实现接口：DefaultConsumer
* getEndpoint，返回TimerEndPoint
* doStart：TimerTask执行逻辑

## TimerComponent

* 实现接口：DefaultComponent
* createEndpoint，返回TimerEndpoint。

