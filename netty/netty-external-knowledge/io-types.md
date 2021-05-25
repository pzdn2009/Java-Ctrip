# IO Types

* **Sync**: user request kernel.
* **Async**: kernel request user.
* **User**: user application
* **Kernel**: OS part.
* **Buffers**:
  * Kernel buffer
  * User buffer

## Blocking IO

* Waiting for kernel IO operations finished , then back to user space.
* Sync
* Flow: 
  * User start read system call,  the user thread will be **blocked**;
  * Kernel receive read system call, and preparing the data. 
    * If no data in kernel buffer,  will be **waiting**.
    * If has data,  then copy data from kernel buffer to user buffer, return
  * When kernel returned,  user thread back to normal.

## Non-Blocking IO

* User request the kernel IO operations, then return immediately.
* Sync
* Flow:
  * User start read system call,  
    * if no data, return failed immediately;
    * if has data, waiting for data ready. Also, user thread blocked.
  * User need request many times to get data from kernel.

## IO Multiplexing

* Reactor
* Sync
* Selector \(Java New IO\) & Epoll \(Linux\) 
* New system call -- Query IO ready Status
  * `select()` examines the I/O descriptor sets whose addresses are passed in readfds, writefds, and errorfds to see if some of their descriptors are ready for reading, are ready for writing, or have an exceptional condition pending, respectively. 
* Flow:
  * register to selector.
  * query ready status by selector. Return the socket ready list.
  * user read system call by the ready one , and user thread blocked.  Kernel copy to user.
  * user back to normal
  * user need to poll the selector. 

## Asynchronous IO

* Kernel call user by call back.
* Callback
* Flow:
  * User request read system call, then back to other things;
  * Kernel do things that waiting data to its buffer, copy data to user buffer  lonely.
  * Kernel send a signal to user,  tell that data ready.
  * User read from user buffer.



