# 锁

zookeeper、redis可以支持分布式锁，分布式锁是分布式事务的核心，但分布式锁不等同于分布式事务。

redis对分布式锁的支持主要通过setnx，在使用redis分布式锁时候，一定要注意处理加锁客户端异常导致锁资源没有正常释放的情况（例如调用端down掉），在调用setnx时候需要加上对锁超时时间的判断。

zookeeper对分布式锁的支持可以直接使用zookeeper curator-recipes客户端

