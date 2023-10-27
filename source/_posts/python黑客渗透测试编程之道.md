---
title: python黑客渗透测试编程之道
date: 2023-07-11 10:54:54
tags:
---



# 简介

​	无论现在还是将来，网络对于黑客而言永远是最重要的竞技场。攻击者总是想方设法通过简单的网络访问做成几乎任何事情，

​	比如扫描主机、注入数据包、嗅探数据、远程攻击主机等等。但是，闯入某个目标企业内网的深处后，你可能并不能为所欲为：执行网络攻击离不开必要的工具。

​	这里没有netcat，没有Wireshark，没有编译器，甚至没有办法去安装编译器。

然而有很多时候，黑客可能会惊讶地发现目标环境里安装了Python。此时，Python就是开路利器

本章将讲授使用Python的socket库进行网络编程的基本知识（完整的socket文档请参见Python官网中的相关页面[1]）。我们会一路编写出客户端、服务端，以及TCP代理。之后我们会把这些组件组装成独创的、自带远程命令功能的netcat。

本章是后续章节的基础，在后面的章节里，我们还会开发主机发现工具、跨平台嗅探工具、远程木马框架等等。我们开始吧



Python网络编程简介Python开发人员可以使用各种第三方工具来创建网络客户端和服务端，但所有这些第三方工具的核心其实都是socket模块。



这个模块提供了所有必需的接口，让你可以快速开发出TCP/UDP客户端、服务端，直接调用原始socket等等。想要攻破目标机器并保持对其的访问权限，其实靠这个socket模块就够了。我们先从编写客户端和服务端开始吧，这两者将会是你最常编写的网络程序。TCP客户端在渗透测试过程中，经常需要创建一个TCP客户端，用来测试服务、发送垃圾数据、进行fuzz等等。如果黑客潜伏在某大型企业的内网环境中，则不太可能直接获取网络工具或编译器，有时甚至连复制/粘贴或者连接外网这种最基本的功能都用不了。

在这种情况下，能快速创建一个TCP客户端将会是一项极其有用的能力。多说无益，我们开始编写代码。下面是一段简单的

## TCP客户端代码

```
import socket

target_host="www.baidu.com"

target_port=80

#创造socket对象
client=socket.socket(socket.AF_INET,socket.SOCK_STREAM)

#连接客户端
client.connect((target_host,target_port))

#发送数据
#字符串前面的"b"在Python中用于表示字节字符串字面值。在Python中，字符串可以表示为文本字符串或字节字符串。
client.send( b"GET / HTTP/1.1\r\nHost: google.com\r\n\r\n")

#接受数据
response = client.recv(4096)

print(response.decode())


client.close()
#首先创建一个带有AF_INET和SOCK_STREAM参数的socket对象❶。AF_INET参数表示我们将使用标准的IPv4地址或主机名，SOCK_STREAM表示这是一个TCP客户端。
#然后，我们将该客户端连接到服务器❷，并发送一些bytes类型的数据❸。最后一步，接收返回的数据并将其打印到屏幕上❹，再关闭socket。这是一个最简单的TCP客户端，
# 也将是你最常写的一段代码。以上代码隐含的几个关于socket的重要假设是你一定要了解的：第一，我们假设所有的连接都会成功，不会出错或发生异常；
# 第二，我们假设服务器期望客户端先发送数据（有的服务器会期望自己先发数据，然后等客户端回复）；第三，我们假设服务器总是能在合理的时间内回应我们。
# 做这些假设主要是为了简化问题，尽管程序员们对于如何处理阻塞、异常之类的情况很有讲究，
#但渗透测试工程师却很少在其又脏又快的攻击代码里纠结于这些花里胡哨的细节，所以本章也会省略这些内容。
```

## UDP客户端代码

```
import socket
target_host = "127.0.0.1"
target_port = 9997
#create a socket object
client = socket.socket( socket.AF_INET, socket.sOCK_DGRAM)

#send some data
client.sendto(b"AAABBBCCC" , (target_host,target_port))

# receive some data
data,addr = client.recvfrom(4096)

print(data.decode())
client.close()

```

​	正如你所看到的，在创建socket对象时，我们把socket类型改成了SOCK_DGRAM❶，之后只要简单调用sendto()函数❷，填好要发送的数据和接收数据的服务器地址就可以了。因为UDP是一个无连接协议，所以开始通信前不需要用connect()函数建立连接。最后一步，是调用recvfrom()函数接收数据❸。你可能注意到了，这个函数不仅会返回接收到的数据，还会返回详细的数据来源信息（主机名与端口号）

## TCP服务端

用Python编写TCP服务端和编写客户端一样简单。你可能会想用自己的TCP服务端做一个远程代码执行工具或者代理工具（这两个需求我们将在后面实现）。

我们先来编写一个标准的多线程TCP服务器，大体的代码结构如下：

```
import socket
import threading
IP = '0.0.0.0'
PORT = 9998
def main():
    server = socket.socket( socket.AF_INET,socket.SOCK_STREAM)
    server.bind((IP,PORT))
    server.listen(5) 
    print(f'[*] Listening on {IP} :{PORT} ')
    while True:
        client,address = server.accept() 
        print(f'[*] Accepted connection from {address[0]}:{address[1]} ')
        client_handler = threading.Thread(target=handle_client,args=(client, ))
        client_handler.start( ) 
def handle_client(client_socket): 

    with client_socket as sock:
      request = sock.recv( 1024)
      print(f'[*] Received: {request.decode( "utf-8")}')
      sock.send (b'ACK ')

if __name__ == '__main___':
    main()

```

作为开场，我们先指定服务器应该监听哪个IP地址和端口❶。接着，让服务器开始监听❷，并把最大连接数设为5。下一步，让服务器进入主循环中，并在该循环中等待外来连接。当一个客户端成功建立连接的时候❸，将接收到的客户端socket对象保存到client变量中，将远程连接的详细信息保存到address变量中。然后，创建一个新的线程，让它指向handle_client函数，并传入client变量。创建好后，我们启动这个线程来处理刚才收到的连接❹，

与此同时服务端的主循环也已经准备好处理下一个外来连接。而handle_client函数❺会调用recv()接收数据，并给客户端发送一段简单的回复。如果用我们之前编写的TCP客户端给服务端发送几个测试数据包，应该会看到以下输出：
