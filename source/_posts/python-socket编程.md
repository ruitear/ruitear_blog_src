---
title: python socket编程
date: 2018-07-22 21:22:12
tags:
  - python
  - socket
categories:
  - 网络编程
---

Socket(套接字)，应用程序通常通过”套接字”向网络发出请求或者应答网络请求，使主机间或者一台计算机上的进程间可以通讯。**服务器端**：`socket()`建立套接字，绑定（bind）并监听（listen），用accept（）等待客户端连接。将accept()写入死循环，每次连接一个客户端，开一个线程。一般情况下建立`socket`连接后服务器与客户端建立了一个管道，当关闭`socket`或关闭客户端的时候，会导致管道破裂信号。如果使用默认处理则会导致服务器程序退出。

##### [](#0-socket函数)0. [socket函数](#socket函数)
```
socket.socket(family, type, protocol)
```

**参数**：

- **family**: 套接字python家族支持`AF_UNIX`、`AF_INET`、`AF_NETLINK`和`AF_INET`

- **type**: 套接字类型可以根据是面向连接的还是非连接分为`SOCK_STREAM`或`SOCK_DGRAM`，其中`SOCK_STREAM`是tcp类型的，`SOCK_DGRAM`是udp类型的

- **protocol**: 一般不填默认为0.

---

##### [](#1-简单的样例通信样例)1. [简单的样例通信样例](#简单的样例通信样例)

###### [](#1-1-TCP服务端)1.1 [TCP服务端](#TCP服务端)

TCP是面向连接的，可靠的流协议。流就是不间断的数据结构

```python
# for python3.X
import socket
s=socket.socket()
host=socket.gethostname()  #获取主机地址,xiangdangyu
port=1234 #有效端口号是0-65535，低于1024的端口号是系统保留的
s.bind((host,port)) #绑定地址（host,port）到套接字， 在AF_INET下,以元组（host,port）的形式表示地址
s.listen(5) #开始TCP监听，操作系统可以挂起的最大连接数量；至少为1，一般设置成5即可
while True:
	c,addr=s.accept() #被动接受TCP客户端连接,(阻塞式)等待连接的到来
	print ("Go",addr)
	c.send(('Thank you for connecting').encode()) #发送TCP数据，将string中的数据发送到连接的套接字
	c.close() #关闭套接字
```

  由于Python2.x和Python3.x的区别，socket通信在3.x版本中需要适应`encode()`和`decode()`编解码,而python3.x最重要的新特性也是对文本和二进制数据做了更清晰的区分。文本用`unicode`编码，为str类型，二进制数据则为bytes类型。`encode`（编码),可以将str类型编码为bytes。`decode`（译码），可以将bytes类型转换为str类型。
  **注意**：如果不添加encode()和decode()就会报`Socket TypeError: a bytes-like object is required, not 'str'`的错误，因此在发送的时候记得将数据`encode()`，接收的时候将数据`decode()`

###### [](#1-2-TCP客户端)1.2 [TCP客户端](#TCP客户端)
```python
import socket
s=socket.socket()
host=socket.gethostname()
port=1234

s.connect((host,port))
data=s.recv(1024).decode() #接收TCP数据，数据以字符串形式返回，bufsize指定要接收的最大数据量。
print (s.recv(1024).decode())
```

###### [](#1-3-UDP服务端)1.3 [UDP服务端](#UDP服务端)

  UDP是一种无连接的，不具有可靠性的数据报文协议
这个是插入时间戳的例子，同理这个还可以在tcp的socket实现，各位看官自己实现吧

```python
# for python3.X
from socket import *
from time import ctime

host = 'localhost'
port = 12345
buffersize = 1024
addr=(host, port)
udpSocket = socket(AF_INET, SOCK_DGRAM)
udpSocket.bind(addr)
while True:
    print('waiting for message...')
    data, addr = udpSocket.recvfrom(buffersize)
    data = data.decode()
    udpSocket.sendto(('[%s] %s' % (ctime(), data)).encode(), addr)
    print('...received form and return to:', addr)

udpSocket.close()
```

###### [](#1-4-UDP客户端)1.4 [UDP客户端](#UDP服务端)
```python
from socket import *
from time import ctime
host='localhost'
port=12345
buffersize=1024
addr=(host, port)
udpClient=socket(AF_INET, SOCK_DGRAM)
while True:
    data = input('>')
    if not data:
        break
    udpClient.sendto(data.encode(), addr) #address是形式为（ipaddr，port）的元组，指定远程地址。返回值是发送的字节数。
    data,addr=udpClient.recvfrom(buffersize)
    data = data.decode()
    if not data:
        break
    print(data)
udpClient.close()
```

  **综上所述**，可以得到这样的结论: `socket`通信，**服务器端** 要开启一个对应**udp**或者**tcp**的`scoket`，在这个`socket`里面指定通信的端口的地址；udp服务器的`socket`这样起，`udpScoket=socket(AF_INET, SOCK_DGRAM)`; 然后在`udpSocket`里面调用`bind()`方法绑定通信的地址`addr=(host, port)`，也就是`udpSocket.bind(addr)`；然后在开启一个循环通过`recvfrom()`方法接收udp数据，`data, addr = udpSocket.recvfrom(buffersize)`，发送出去的话就利用`sendto()`方法，`udpSocket.sendto(('[%s] %s' % (ctime(), data)).encode(), addr)`。tcp的`socket`相对于udp的socket是一种可靠的传输，在起`socket`的时候不同于udp的主要有两点，一是利用`SOCK_STREAM`，二是tcp的通信需要在一段时间内监听通信端口是否有数据发来，这是tcp协议所特有的。**客户端** 服务器端开启了`socket`服务之后，`socket`客户端需要对应服务器开启一个对应的`socket`客户端：tcp开`udpClient=socket(AF_INET, SOCK_STREAM)`，然后让这个客户端的`socket`连接到服务器的`socket`；也就是`tcpClient.connect(ADDR)' 这里ADDR=(host, port)`udpClient=socket(AF_INET, SOCK_DGRAM)`这样客户端就和服务器连接起来了；客户端样用`recv()`方法接收服务器的数据；用`send()`方法将数据发送到服务器。udp客户同tcp的方法一样，跟对应模式的`socket`方法一直，端口对端口，地址对地址。

---

##### [](#2、socketserver模块)2、[socketserver模块](#socketServer模块)

  `socketserver`是标准库中的一个高级模块，用于网络客户端与服务器的实现，模块定义了一些类来处理诸如TCP、UDP、UNIX流和UNIX数据报之上的同步网络请求。；python3中，导入`socketserver`模块, 使用`import socketserver`. 在使用socketserver的时候必须说先定义一个继承`BaseRequestHandler`的处理类。

> 
**`socketserver` 模块中包含的类**：

- `BaseServer`   包含服务器的核心功能与混合类(min-in)的钩子功能.这个类主用于派生,不要直接生成这个类的类对象,可以考虑使用;

- `TCPServer/UDPServer`   `TCPServer`基本的网络同步tcp服务器/`UDPServer`   基本的网络同步udp服务器;

- `ForkingMinIn`   实现了核心的进程化功能,用于与服务器类进行混合(min-in),以提供一些异步特性.不要直接生成这个类的对象;

- `ThreadingMinIn` 实现了核心的线程化功能,用于与服务器类进行混合(min-in),以提供一些异步特性,不要直接生成这个类的对象;

- `ForkingTCPServer/ForkingUDPServer`  `ForkingMinIn`与`TCPServer`的组合/`ForkingMinIn` 与 `UDPServer`的组合;

- `BaseRequestHandler`  无法创建这个类的实例，可以使用 `StreamRequestHandler` 和 `DataStreamRequestHandler` 创建类的实例； 

- `StreamRequestHandler/DataStreamRequestHandler`  tcp请求处理类的一个实现/udp请求处理类的一个实现;

##### [](#socket线程池)socket线程池

##### [](#附表)[附表](#附表)

socket属性模块

> 
**服务器端套接字*通用函数**

| 函数名 | 函数名 |
|---|---|
| s.bind() | 绑定地址（host,port）到套接字，在AF_INET下,以元组（host,port）的形式表示地址 |
| s.listen() | 开始TCP监听。backlog指定在拒绝连接之前，操作系统可以挂起的最大连接数量。该值至少为1，大部分应用程序设为5就可以了 |
| s.accept() | 被动接受TCP客户端连接,(阻塞式)等待连接的到来客户端套接字 |
| s.connect() | 主动初始化TCP服务器连接，一般address的格式为元组（hostname,port）,如果连接出错，返回socket.error错误 |
| s.connect_ex() | 函数的扩展版本,出错时返回出错码,而不是抛出异常 |

  

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
