# BeanComponent

URI: bean:beanID\[?options\]

* beanID：.

Options:

* ref String
* method String 
* beanType String 
* cache Boolean

## BeanEndpoint

元数据：

* beanName
* method
* cache
* parameters

辅助（transient）数据：

* beanHolder
* processor

方法：

* createProducer，返回BeanProducer；
* createConsumer，不支持，即没有消费者。
* isSingleton，true，表示是单例模式；
* start，初始化Bean。
* stop.

## BeanProducer

## BeanComponent

元数据：

* beanInfoCache，提供bean的缓存。
* BeanInfo，封装了一个Bean的元数据

方法：

* createEndpoint，返回TimerEndpoint。

## BeanProcessor

