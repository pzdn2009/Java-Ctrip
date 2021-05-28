# Network IO -- Socket

Ref:

{% embed url="https://www.jianshu.com/p/7ac69db65a0e" %}

## Socket API

```c
            Server                          Client

            socket()
               |
            bind()
               |
            listen()                        socket()
               |                              |
            accept()                          |
               |                              |
               | <------------------------- connect()
               |                              |
            read() <------------------------ write()
               |                              |
            write() -----------------------> read()
               |                              |
            close()                         close()

// creates an endpoint for communication and returns a descriptor.
int socket(int domain, int type, int protocol);

// bind a name to a socket
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);

//  listen for connections on a socket
int listen(int sockfd, int backlog);

// accept a connection on a socket
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);

// read the input 
ssize_t read(int fd, void *buf, size_t count);

// write the output
ssize_t write(int fd, const void *buf, size_t count);

// initiate a connection on a socket
int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);

// delete a descriptor
int close(int fd);
```

Blocking

* accept

### socket

创建套接字，指定要使用的地址簇\(ip\)和套接字类型/传输层协议\(tcp\)，返回一个`套接字描述符`，简称`sockfd`，用于在函数调用\(connect、read 等\)中标识这个套接字。

### bind

将一个 本地IP:本地端口 赋值给一个套接字，限定该套接字只接收目的地为指定的本地IP、端口的客户连接。进程指定。

### listen

仅由 TCP server 调用。当 socket 函数创建了套接字，会假设为`主动套接字`，也就是将调用 connect 主动发起连接的套接字。linsen 将一个未连接的套接字转换成`被动套接字`，通知内核应该接受指向该套接字的连接请求\(被动打开\)。此时套接字的状态从 CLOSED 转到 LISTEN。

内核为每个监听套接字维护两个队列：

* 未完成连接队列，收到了客户的 SYN，正在等待 TCP 三次握手完成。此时套接字的状态为 SYN\_RCVD。
* 已完成连接队列，已经完成 TCP 三次握手的客户，这些套接字处于 ESTABLISHED 状态。

当收到客户的SYN，TCP在未完成连接队列中创建一项新条目，同时继承监听套接字的参数，然后返回SYS、ACK，这一项一直保留在未完成连接队列中，直到收到 ACK，或者该项超时。如果三次握手正常完成，该项从未完成连接队列移到已完成连接队列的队尾。

指定 backlog，即两个队列长度之和。

  


![](../../.gitbook/assets/image%20%289%29.png)

### accept

仅由 TCP server 调用，从已完成连接的队头获得一个已完成连接，如果已完成连接的队列为空，进程被挂起进入睡眠状态\(假定套接字为默认的阻塞方式\)，直到队列中有条目。

接收监听套接字描述符，返回 由内核自动为获得的已完成连接生成的`已连接套接字描述符`，以及对应的`已连接套接字`。

### connect

仅由 TCP client 调用，接收 socket 返回的套接字描述符，主动向 TCP server 发送建立连接的请求\(主动打开\)，触发三次握手。调用 connect 之前不一定要调用 bind 绑定本地ip 和 本地端口，这样，内核会通过数据出口确定源ip，并选定一个临时端口作为源端口。

### read

read data from kernel buffer to user buffer.

### write

write data to kernel buffer from user buffer.

### close

当套接字描述符的引用为0，发送 FIN 关闭套接字，终止 TCP 连接。

默认行为是将该套接字标记为已关闭，然后立即返回。这个套接字的描述符不能再被 read 或 write 使用。

## Picture

![Tcp Status](../../.gitbook/assets/image%20%285%29.png)

![Client-Server Socket Communication](../../.gitbook/assets/image%20%288%29.png)

![With The three-way handshake.](../../.gitbook/assets/image%20%287%29.png)

