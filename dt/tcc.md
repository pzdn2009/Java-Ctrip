# TCC

RESTful TCC模式分3个阶段执行

* Trying阶段主要针对业务系统检测及作出预留资源请求, 若预留资源成功, 则返回确认资源的链接与过期时间
* Confirm阶段主要是对业务系统的预留资源作出确认, 要求TCC服务的提供方要对确认预留资源的接口实现幂等性, 若Confirm成功则返回204, 资源超时则证明已经被回收且返回404
* Cancel阶段主要是在业务执行错误或者预留资源超时后执行的资源释放操作, Cancel接口是一个可选操作, 因为要求TCC服务的提供方实现自动回收的功能, 所以即便是不认为进行Cancel, 系统也会自动回收资源

Ref：[http://blog.csdn.net/Solarison/article/details/68061157](http://blog.csdn.net/Solarison/article/details/68061157) [https://www.atomikos.com/Blog/TCCForTransactionManagementAcrossMicroservices](https://www.atomikos.com/Blog/TCCForTransactionManagementAcrossMicroservices) [https://github.com/prontera/spring-cloud-rest-tcc](https://github.com/prontera/spring-cloud-rest-tcc)

