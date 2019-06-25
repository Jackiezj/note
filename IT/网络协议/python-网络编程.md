## 一、网络编程

### 1. 网络通信

> 网络就是一种辅助双方或者多方能够连接在一起的工具

- **使用网络的目的**
  联通多方进行通信  即把数据从一方传递给另外一方

  网络编程 :  让在不同的电脑上的软件能够进行数据传递,即进程之间的通信

### 2. IP地址

> ip地址:用来在网络中标记一台电脑,比如192.168.1.1;在本地局域网上是唯一的。

> IP地址127.0.0.1~127.255.255.255用于回路测试,	127.0.0.1代表本机IP地址

### 3. Linux网络命令

- **ifconfig**

  > 查看或配置网卡信息:ifconfig

  修改ip

  ```bash
  sudo ifconfig wlp0s20f3 192.168.199.100
  ```

- **ping**

  > 测试远程主机连通性

  ```bash
  ping 192.168.1.1
  ping www.baidu.com
  ```

### 4. 端口

> 如果一个程序需要收发网络数据,那么就需要有这样的端口
> 在linux系统中,端口可以有65536(2的16次方)个之多!

- **端口号**

  端口是通过端口号来标记的,端口号只有整数,范围是从0到65535

- **端口是怎样分配的**

  > 端口号不是随意使用的,而是按照一定的规定进行分配。

  - 知名端口(Well Known Ports)

    范围从0到1023

    ​	80端口分配给HTTP服务
    ​	21端口分配给FTP服务

    > 一般情况下,如果一个程序需要使用知名端口的需要有root权限

    

  - 动态端口(Dynamic Ports)

    动态端口的范围是从1024到65535

    > 动态分配是指当一个系统程序或应用程序程序需要网络通信时,它向主机申请一个端口,主机从可用的端口号中分配一个供它使用
    >
    > 当这个程序关闭时,同时也就释放了所占用的端口号



- 查看端口

  ```bash
  netstat -an
  ```

- 端口作用

  通过 “IP地址 + 端口号”区分不同的服务

  **总而言之,IP地址标识网络中的一台主机,端口号标识这台主机上的一个服务(应用程序)**

### 5. socket

- **进程** : 指的是**运行的程序**以及运行时**用到的资源**这个**整体**称之为进程

- **进程间通信** : 指的是:运行的程序之间的实现**数据共享和传递**

- **socket**(简称套接字) ：是最通用的**进程间通信的一种方式**

  它与其他进程间通信的一个主要不同是 : 它能实现**不同主机间的进程间通信**,我们网络上各种各样的服务大多都是基于Socket来完成通信的

#### 1) 什么是socket

- socket图解

  > **通信前提** : 完成通信需要**一对套接字**(socket),网络通信的一端称为一个socket
  > **本质** : 对底层网络协议**TCP/IP的封装**	并且提供了一套应用程序接口(**API**)
  > 学习socket编程的**目的** : 用这一套函数接口为我们的应用**开发服务**。

#### 2) 创建socket

> 在Python中使用socket模块的函数socket就可以完成:

```python
import socket
socket.socket(AddressFamily, Type)
```

> 说明:
> **socket.socket** : 创建一个socket,该函数带有两个参数:
> **AddressFamily** : 可以选择AF_INET(用于Internet进程间通信表示IPv4协议)或者AF_INET6(表示IPv6协议未来很快可能用上)等
> **Type** : 套接字类型,可以是SOCK_DGRAM(数据报套接字,主要用于UDP协议)或者SOCK_STREAM(流式套接字,主要用于TCP协议)等

#### 3) 创建一个udp socket(udp套接字)

```python
import socket
# 创建udp的套接字
s =	socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
# ...这里是使用套接字的功能(省略)...
# 不用的时候,关闭套接字
s.close()
```

#### 4) 创建一个tcp socket(tcp套接字)

```python
import	socket
# 创建tcp的套接字
s =	socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# ...这里是使用套接字的功能(省略)...
# 不用的时候,关闭套接字
s.close()
```

> 说明:
> 	套接字使用流程与文件的使用流程很类似
>
> 1. 创建套接字
> 2. 使用套接字收/发数据
> 3. 关闭套接字

## 二、udp服务器

### 1. udp-接收数据

> 创建一个基于udp的网络程序流程很简单,具体步骤如下:
>
> 1. 创建客户端套接字
> 2. 发送/接收数据
> 3. 关闭套接字

- 示例

  ```python
  from socket import *
  # 创建socket
  udp_socket = socket(AF_INET, SOCK_DGRAM)
  # 绑定端口
  udp_socket.bind(("", 9999))  #	ip地址和端口号,ip一般不用写,表示本机的任何一个ip
  # 发送数据
  udp_socket.sendto((input("请输入要发送的数据")).encode('utf-8'), ("127.0.0.1", 8888))
  # 关闭socket
  udp_socket.close()
  ```

  

### 2. udp-发送-接收数据

- 示例

  ```python
  from socket import *
  # 创建socket
  udp_socket = socket(AF_INET, SOCK_DGRAM)
  # 绑定端口
  udp_socket.bind(("", 8888))
  # 接收数据
  recv = udp_socket.recvfrom(1024)
  recv_data = recv[0].decode('utf-8')
  client_socket = recv[1]
  # 发送数据
  udp_socket.sendto(("拉收到了数据" + recv_data).encode('utf-8'), ("127.0.0.1", 9999))
  # 关闭socket
  udp_socket.close()
  ```

  - 编解码问题

  > 其中decode()与encode()方法**可以接受参数**,其声明分别为:
  > bytes.decode(encoding="utf-8", errors="strict")
  > str.encode(encoding="utf-8", errors="strict")
  > **encoding**是指在解码/编码(动词)过程中使用的字符编码(名词)
  > **errors**是指错误的处理方案,errrors参数默认值是strict(严格的)意味着如果编解码出错将会抛出
  > UnicodeError;
  > 如果想忽略编解码错误可以将errors设置为ignore。

### 3. 案例-udp聊天室

```python
from socket import *


def send_msg(udp_socket):
    udp_socket.sendto(input("请输入要发送的数据").encode("utf-8"), ("", 8888))


def recv_msg(udp_socket):
    recv = udp_socket.recvfrom(1024)
    recv_data = recv[1]
    recv_socket = recv[0]
    print(recv_data, recv_socket)


def main():
    udp_socket = socket(AF_INET, SOCK_DGRAM)
    udp_socket.bind(("", 9999))
    while True:
        choose_num = input("请输入功能： 1.接收数据， 2.发送数据")
        if choose_num == "2":
            send_msg(udp_socket)
        elif choose_num == "1":
            recv_msg(udp_socket)
        else:
            print("输入有误，请重新输入")


if __name__ == '__main__':
    main()
```

## 三、TCP服务器

### 1. 什么是tcp

> **TCP协议 : 传输控制协议**(Transmission Control Protocol)
>
> 一种面向连接的、可靠的、基于字节流的传输层通信协议

### 2. TCP特点

- **面向连接**

  TCP通信需要经过创建连接、数据传送、终止连接三个步骤。

  通信双方必须先建立连接才能进行数据的传输双方都必须为该连接分配必要的系统内核资源,以管
  理连接的状态和连接上的传输。

  双方间的数据传输都可以通过这一个连接进行

  完成数据交换后,双方必须断开此连接,以释放系统资源

  **这种连接是一对一的,因此TCP不适用于广播的应用程序,基于广播的应用程序请使用UDP协议。**

- **可靠传输**

  - TCP采用发送应答机制

    TCP发送的每个报文段都必须得到接收方的应答才认为这个TCP报文段传输成功

  - 超时重传

    发送端发出一个报文段之后就启动定时器,如果在定时时间内没有收到应答就重新发送这个报文段。

    TCP为了保证不发生丢包,就给每个包一个序号,同时序号也保证了传送到接收端实体的包的按序接
    收。然后接收端实体对已成功收到的包发回一个相应的确认(ACK);如果发送端实体在合理的往返
    时延(RTT)内未收到确认,那么对应的数据包就被假设为已丢失将会被进行重传。

  - 错误校验
    TCP用一个校验和函数来检验数据是否有错误;在发送和接收时都要计算校验。

  - 流量控制和阻塞管理
    流量控制用来避免主机发送得过快而使接收方来不及完全收下。



### 3. TCP与UDP的不同点

- 面向连接(确认有创建三方交握,连接已创建才作传输。)
- 有序数据传输
- 重发丢失的数据包
- 舍弃重复的数据包
- 无差错的数据传输
- 阻塞/流量控制

而udp通信模型中,在通信开始之前,不需要建立相关的链接,只需要发送数据即可,类似于生活中,"写信"/“发短信“

### 4. tcp客户端

- 示例

  ```python
  from socket import *
  
  # 创建tcp_socket
  tcp_socket = socket(AF_INET, SOCK_STREAM)
  tcp_socket.bind(("", 8888))
  # 连接到服务器
  tcp_socket.connect(("192.168.199.113", 9999))
  # 发送数据
  tcp_socket.send("客户端数据".encode('utf-8'))
  # 接收数据
  recv_data = tcp_socket.recv(1024)
  print(recv_data.decode('utf-8'))
  # 关闭socket
  tcp_socket.close()
  ```

  

### 5. tcp服务器

- 流程

  1. socket创建一个套接字
  2. bind绑定ip和port
  3. listen使套接字变为可以被动套接字
  4. accept取出一个客户端连接用以服务
  5. recv/send接收发送数据

- 示例

  ```python
  from socket import *
  # 创建server_socket
  server_socket = socket(AF_INET, SOCK_STREAM)
  # 绑定ip端口
  server_socket.bind(("192.168.199.113", 9999))
  # 被动监听
  server_socket.listen(128)
  # 连接客户端进行通信 （阻塞）
  client_socket, client_addr = server_socket.accept()
  
  # 接收数据
  recv_data = client_socket.recv(1024)
  # 发送数据
  client_socket.send("tcp的服务器的数据".encode('utf-8'))
  
  # 关闭socket
  client_socket.close()
  ```

### 6. tcp注意

1. tcp服务器一般情况下都需要绑定,否则客户端找不到这个服务器
2. tcp客户端一般不绑定,因为是主动链接服务器,所以只要确定好服务器的ip、port等信息就好,本
   地客户端可以随机
3. tcp服务器中通过listen可以将socket创建出来的主动套接字变为被动的,这是做tcp服务器时必须要
   做的
4. 当客户端需要链接服务器时,就需要使用connect进行链接,udp是不需要链接的而是直接发送,但
   是tcp必须先链接,只有链接成功才能通信
5. 当一个tcp客户端连接服务器时,服务器端会有1个新的套接字,这个套接字用来标记这个客户端,
   单独为这个客户端服务
6. listen后的套接字是被动套接字,用来接收新的客户端的连接请求的,而accept返回的新套接字是标
   识这个新客户端的
7. 关闭listen后的套接字意味着被动套接字关闭了,会导致新的客户端不能够链接服务器,但是之前
   已经链接成功的客户端正常通信。
8. 关闭accept返回的套接字意味着这个客户端已经服务完毕
9. 当客户端的套接字调用close后,服务器端会recv解阻塞,并且返回的长度为0,因此服务器可以通
   过返回数据的长度来区别客户端是否已经下线;同理	当服务器断开tcp连接的时候	客户端同样也会
   收到0字节数据。

### 7. 案例：文件下载器

- 服务器代码示例

  ```python
  from socket import *
  
  
  def init():
      # 创建socket
      server_socket = socket(AF_INET, SOCK_STREAM)
      server_socket.bind(("192.168.199.113", 9999))
      server_socket.listen(128)
      return server_socket
  
  
  def conn_client(server_socket):
      return server_socket.accept()
  
  
  def send_smg(client_socket, content):
      client_socket.send(content.encode("utf-8"))
  
  
  def recv_smg(client_socket):
      return client_socket.recv(1024)
  
  
  def read_file(file_name):
      with open(file_name, "r") as f:
          return f.read()
  
  
  def main():
      server_socket = init()
      client_socket, client_addr = conn_client(server_socket)
      recv_data = recv_smg(client_socket)
      # 根据接收到的数据返回文件
      file_name = recv_data.decode("utf-8")
      file_content = read_file(file_name)
      # 发送响应
      send_smg(client_socket, file_content)
  
  
  if __name__ == '__main__':
      main()
  ```

- 客户端代码示例

  ```python
  from socket import *
  
  
  def main():
      client_socket = socket(AF_INET, SOCK_STREAM)
      client_socket.connect(("192.168.199.113", 9999))
      file_name = input("请输入文件名：")
      client_socket.send(file_name.encode("utf-8"))
      recv_data = client_socket.recv(1024)
      if recv_data:
          with open("[接收文件]"+file_name, "w") as f:
              f.write(recv_data.decode("utf-8"))
      client_socket.close()
  
  
  if __name__ == '__main__':
      main()
  ```

  

## 四、tcp-ip协议

> 计算机都遵守的**网络通信协议**叫做 **TCP/IP协议**

- **socket套接字底层封装的就是这个TCP/IP协议**做的事情
- 因为互联网协议包含了上百种协议标准,但是最重要的两个协议是TCP和IP协议
- 把互联网的协议简称TCP/IP协议(族)

> 网际层也称为: 网络层
> 网络接口层也称为: 链路层



## 五、HTTP协议

### 1. 网络通信过程

1. 在浏览器中输入一个网址时,需要将它先解析出ip地址来

2. 当得到ip地址之后,浏览器以tcp的方式3次握手链接服务器

3. 以tcp的方式发送http协议的请求数据给服务器

4. 服务器tcp的方式回应http协议的应答数据	给浏览器




- 一些概念
  - MAC地址:在设备与设备之间数据通信时用来标记收发双方(网卡的序列号)
  - IP地址:在逻辑上标记一台电脑,用来指引数据包的收发方向(相当于电脑的序列号)
  - 网络掩码:用来区分ip地址的网络号和主机号
  - 默认网关:当需要发送的数据包的目的ip不在本网段内时,就会发送给默认的一台电脑,成为网关
  - 集线器:已过时,用来连接多态电脑,缺点:每次收发数据都进行广播,网络会变的拥堵
  - 交换机:集线器的升级版,有学习功能知道需要发送给哪台设备,根据需要进行单播、广播
  - 路由器:连接多个不同的网段,让他们之间可以进行收发数据,每次收到数据后,ip不变,但是
  - MAC地址会变化
  - DNS:根据域名用来解析出IP(类似电话簿)
  - http服务器:提供浏览器能够访问到的数据

### 2. HTTP协议

> HTTP: 超文本传输协议(HyperText Transfer Protocol) 是一种应用层协议
>
> HTML是一种用来**定义网页的文本**





















