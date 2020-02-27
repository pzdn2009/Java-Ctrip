# Locks

* Shared:InterProcessMutex 
* SharedReadWriteLock:InterProcessReadWriteLock 
* 信號量

\*多共享锁:内部构件多个共享锁\(会跟一个znode path关联\), 在acquire\(\)过程中,

* 执行所有共享锁的acquire\(\)方法, 如果中间出现一个失败, 则将释放所有已require的共享锁; 
* 执行release\(\)方法时, 则执行内部多个共享锁的release方法\(如果出现失败将忽略\) 

## shared lock

共享锁: 全局同步分布式锁, 同一时间两台机器只有一台能获得同一把锁.

```java
    private static void sharedLock(){
        CuratorFramework client = getClient();        
        InterProcessMutex sharedLock = new InterProcessMutex(client, "/sharedlock");
        try {
              if(sharedLock.acquire(50, TimeUnit.MILLISECONDS)){
                System.out.println(Thread.currentThread().getName() + "  is  get the shared lock");
                Thread.sleep(100000);
                System.out.println(Thread.currentThread().getName() + "  is  release the shared lock");
            }

        } catch (Exception e) {            
        }finally {
            try {
                System.out.println(Thread.currentThread().getName() + "  the flag is " + sharedLock.isAcquiredInThisProcess());
                if(sharedLock.isAcquiredInThisProcess())//判断是否持有锁 进而进行锁是否释放的操作
                sharedLock.release();
            } catch (Exception e) {
                e.printStackTrace();
            }

        }
    }
```

## read write lock

共享读写锁: 用于分布式的读写互斥处理, 同时生成两个锁:一个读锁, 一个写锁,读锁能被多个应用持有, 而写锁只能一个独占, 当写锁未被持有时, 多个读锁持有者可以同时进行读操作

```java
 private static void testReadWriterLock(){
        CuratorFramework client = getClient();

        InterProcessReadWriteLock readWriteLock = new InterProcessReadWriteLock(client, "/readwriter");
       final  InterProcessMutex readLock = readWriteLock.readLock();
       final  InterProcessMutex writeLock = readWriteLock.writeLock();

        List<Thread> jobs = new ArrayList<Thread>();

        for (int i = 0; i < 20; i++) {
            Thread t = new Thread("写锁  " + i){                
                public void run(){
                    readWriterLock(writeLock);
                }
            };
            jobs.add(t);
        }

        for (int i = 0; i < 1; i++) {
            Thread t = new Thread("读锁  " + i){

                public void run(){
                    readWriterLock(readLock);
                }
            };
            jobs.add(t);
        }

        for (Thread thread : jobs) {
            thread.start();
        }

    }

    private static void readWriterLock(InterProcessLock lock){
        System.out.println(Thread.currentThread().getName()+"   进入任务 " + System.currentTimeMillis());
        try {
            if(lock.acquire(20, TimeUnit.MILLISECONDS)){
            //执行任务 --读取 或者写入
               int time = RandomUtils.nextInt(150);

               System.out.println(Thread.currentThread().getName()+"   执行任务开始");
               Thread.sleep(time);
              System.out.println(Thread.currentThread().getName()+"   执行任务完毕");
            }else{
              System.err.println(Thread.currentThread().getName() + " 等待超时  无法获取到锁"); 
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            try {
                if(lock.isAcquiredInThisProcess())
                    lock.release();
            } catch (Exception e2) {

            }
        }

    }
```

## 信號量

共享信号量: 在分布式系统中的各个JVM使用同一个zk lock path, 该path将跟一个给定数量的租约\(lease\)相关联, 然后各个应用根据请求顺序获得对应的lease, 相对来说, 这是最公平的锁服务使用方式.

```java
    private static void testSharedSemaphore(final int x){
        CuratorFramework client = getClient();
        final InterProcessSemaphoreV2 semaphoreV2 = 
               /// new InterProcessSemaphoreV2(client, "/sharedsemaphore", 50);
                new InterProcessSemaphoreV2(client, "/sharedsemaphore",
                        new SharedCount(client, "/semaphore", 2));
        List<Thread> jobs  = new ArrayList<Thread>();
        for (int i = 0; i < 2; i++) {
            Thread thread = new Thread(x +"　　共享信息锁 " + i){
                public void run(){
                    sharedSemaphore(semaphoreV2);
                }
            };
            jobs.add(thread);
        }
        for (Thread thread : jobs) {
            thread.start();
        }
    }


    /**
     * 共享信号量
     * 设置总的数量 -->分布式情况下的最大并行数量
     * 按照请求顺序进行 执行权的分配
     * 可以设置超时 不执行 也可以设置 直到获取执行权 执行
     */
    private static void sharedSemaphore(InterProcessSemaphoreV2 semaphoreV2){

        Lease lease = null;
        try {
           // lease = semaphoreV2.acquire(10, TimeUnit.MILLISECONDS);
            lease = semaphoreV2.acquire();
            if(lease != null){
                System.out.println(Thread.currentThread().getName()+"   执行任务开始" + System.currentTimeMillis());
                //Thread.sleep(RandomUtils.nextInt(4000));
                Thread.sleep(1000);
                System.out.println(Thread.currentThread().getName()+"   执行任务完毕" + System.currentTimeMillis());
            }

        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                if(lease !=null)
                    semaphoreV2.returnLease(lease);
            } catch (Exception e2) {
                e2.printStackTrace();
            }
        }
    }
```

