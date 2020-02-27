# EndPoint

实现了EndPoint EIP，可以接收和发送Message exchanges.

Endpoints are usually created by a Component and Endpoints are usually referred to in the DSL via their URIs.

From an Endpoint you can use the following methods

* **createProducer**\(\) will create a Producer for sending message exchanges to the endpoint
* **createConsumer**\(\) implements the Event Driven Consumer pattern for consuming message exchanges from the endpoint via a Processor when creating a Consumer
* **createPollingConsumer**\(\) implements the Polling Consumer pattern for consuming message exchanges from the endpoint via a PollingConsumer
* **createExchange**\(\)

为什么是Message exchanges？

* Exchange有三种：InOnly, InOut, InOptionalOut
* 加上Message（id,header,body,attachment）

## 相关接口

* IsSingleton
* Service

```java
public interface Endpoint extends IsSingleton, Service {

    /**
     * Returns the string representation of the endpoint URI
     *
     * @return the endpoint URI
     */
    String getEndpointUri();

    /**
     * Returns a string key of this endpoint.
     * <p/>
     * This key is used by {@link org.apache.camel.spi.LifecycleStrategy} when registering endpoint.
     * This allows to register different instances of endpoints with the same key.
     * <p/>
     * For JMX mbeans this allows us to use the same JMX Mbean for all endpoints that are logical
     * the same but have different parameters. For instance the http endpoint.
     *
     * @return the endpoint key
     */
    String getEndpointKey();

    /**
     * Create a new exchange for communicating with this endpoint
     *
     * @return a new exchange
     */
    Exchange createExchange();

    /**
     * Create a new exchange for communicating with this endpoint
     * with the specified {@link ExchangePattern} such as whether its going
     * to be an {@link ExchangePattern#InOnly} or {@link ExchangePattern#InOut} exchange
     *
     * @param pattern the message exchange pattern for the exchange
     * @return a new exchange
     */
    Exchange createExchange(ExchangePattern pattern);

    /**
     * Returns the context which created the endpoint
     *
     * @return the context which created the endpoint
     */
    CamelContext getCamelContext();

    /**
     * Creates a new producer which is used send messages into the endpoint
     *
     * @return a newly created producer
     * @throws Exception can be thrown
     */
    Producer createProducer() throws Exception;

    /**
     * Creates a new producer which is used send messages into the endpoint
     *
     * @return a newly created producer
     * @throws Exception can be thrown
     */
    AsyncProducer createAsyncProducer() throws Exception;

    /**
     * Creates a new <a
     * href="http://camel.apache.org/event-driven-consumer.html">Event
     * Driven Consumer</a> which consumes messages from the endpoint using the
     * given processor
     *
     * @param processor  the given processor
     * @return a newly created consumer
     * @throws Exception can be thrown
     */
    Consumer createConsumer(Processor processor) throws Exception;

    /**
     * Creates a new <a
     * href="http://camel.apache.org/polling-consumer.html">Polling
     * Consumer</a> so that the caller can poll message exchanges from the
     * consumer using {@link PollingConsumer#receive()},
     * {@link PollingConsumer#receiveNoWait()} or
     * {@link PollingConsumer#receive(long)} whenever it is ready to do so
     * rather than using the <a
     * href="http://camel.apache.org/event-driven-consumer.html">Event
     * Based Consumer</a> returned by {@link #createConsumer(Processor)}
     *
     * @return a newly created pull consumer
     * @throws Exception if the pull consumer could not be created
     */
    PollingConsumer createPollingConsumer() throws Exception;

    /**
     * Configure properties on this endpoint.
     * 
     * @param options  the options (properties)
     */
    void configureProperties(Map<String, Object> options);

    /**
     * Sets the camel context.
     *
     * @param context the camel context
     */
    void setCamelContext(CamelContext context);

    /**
     *  是否为宽松属性
        Should all properties be known or does the endpoint allow unknown options?
     * <p/>
     * <tt>lenient = false</tt> means that the endpoint should validate that all
     * given options is known and configured properly.
     * <tt>lenient = true</tt> means that the endpoint allows additional unknown options to
     * be passed to it but does not throw a ResolveEndpointFailedException when creating
     * the endpoint.
     * <p/>
     * This options is used by a few components for instance the HTTP based that can have
     * dynamic URI options appended that is targeted for an external system.
     * <p/>
     * Most endpoints is configured to be <b>not</b> lenient.
     *
     * @return whether properties is lenient or not
     */
    boolean isLenientProperties();
}
```

* 获取URI
* 配置终结点的属性
* 创建Exchange，Producer，Consumer

