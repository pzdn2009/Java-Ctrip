# CamelContext

## 相关接口

* Service接口；
  * start
  * stop
  * init
  * 即表示CamelContext也是服务的一种。
* SuspendableService接口；支持暂停。
  * suspend
  * resume
  * isSuspended
* RuntimeConfiguration；
* StartupListener；监听器

## CamelContext

功能：

* 是整个Camel的核心，管理Routes,Component，Service，EndPoint,Processor,Registry,Transformer,Validator等。

生命周期分析：通过start，stop，suspend,resume来控制。

```java
public interface CamelContext extends SuspendableService, RuntimeConfiguration {

    void start() throws Exception;

    void stop() throws Exception;

    String getName();

    CamelContextNameStrategy getNameStrategy();

    void setNameStrategy(CamelContextNameStrategy nameStrategy);

    ManagementNameStrategy getManagementNameStrategy();

    void setManagementNameStrategy(ManagementNameStrategy nameStrategy);

    String getManagementName();

    void setManagementName(String name);

    String getVersion();

    ServiceStatus getStatus();

    String getUptime();

    long getUptimeMillis();
```

* start: 启动CamelContext，这个方法不会被阻塞
* stop：will stop all routes/components/endpoints etc and clear internal state/cache
* ServiceStatus：**Starting, Started, Stopping, Stopped, Suspending, Suspended**。
* addService
* removeService
* hasService
* addStartupListener
* addComponent
* hasComponent
* getComponent
* getComponentNames
* removeComponent
* getEndpoint

