...menustart

- [服务器sleep, 客户端一直发送消息, 会发生什么](#6d1ea01ce795012bfdb610364153e7bb)
- [粘包和拆包](#af6f496156931a0c679503a97f372f07)
- [分包](#dc8bbdca8754eda944cf5d634af781b5)
- [粘包与分包解决方法](#4de8e4ec4db5a29659c3f35f636aa421)
- [网络设备工作在哪层？](#d12e572ac98a9695d8835393fefd717a)
- [分片传输数据](#4a59949b01af57900c573cc1a578b8c3)
- [Difference of Hub / Switch / Router](#8f4f3bfa233c4b26a13483f4f73860e0)

...menuend


<h2 id="6d1ea01ce795012bfdb610364153e7bb"></h2>


# 服务器sleep, 客户端一直发送消息, 会发生什么

1. 阻塞TCP
    - 双方内核的套接字缓冲区很快被填满， 客户端进程进入 睡眠
2. 非阻塞TCP
    - 双方内核的套接字缓冲区很快被填满， 客户端后续 write 返回错误
3. UDP
    - UDP套接字没有真正的发送缓冲区
    - 对于一个阻塞的UDP套接字（默认设置），write函数调用将不会因为与TCP套接字一样的原因而阻塞，不过有可能会因为其他原因而阻塞。 


<h2 id="af6f496156931a0c679503a97f372f07"></h2>


# 粘包和拆包 

- UDP
    - UDP协议的保护消息边界使得每一个消息都是独立的
    - UDP不存在粘包问题，是由于UDP发送的时候，没有经过Negal算法优化，不会将多个小包合并一次发送出去。
        - 另外，在UDP协议的接收端，采用了链式结构来记录每一个到达的UDP包，这样接收端应用程序一次recv只能从socket接收缓冲区中读出一个数据包。
        - 也就是说，发送端send了几次，接收端必须recv几次（无论recv时指定了多大的缓冲区）。
    - UDP最大载荷为1472，因此最好能 每次传输接近这个数的数据量


- TCP
    - TCP流传输,把数据当作一串数据流,他不认为数据是一个一个的消息. 
    - 如果发送的网络数据包太小，那么他本身会启用Nagle算法（可配置是否启用）对较小的数据包进行合并（基于此，TCP的网络延迟要UDP的高些）然后再发送（超时或者包大小足够）
    - 对于数据传输频繁的程序来讲，使用TCP可能会容易粘包。
    - 当然，对接收端的程序来讲，如果机器负荷很重，也会在接收缓冲里粘包。这样，就需要接收端额外拆包，增加了工作量。

<h2 id="dc8bbdca8754eda944cf5d634af781b5"></h2>


# 分包

- 分包产生的原因就简单的多：可能是IP分片传输导致的，也可能是传输过程中丢失部分包导致出现的半包，还有可能就是一个包可能被分成了两次传输，在取数据的时候，先取到了一部分（还可能与接收的缓冲区大小有关系），总之就是一个数据包被分成了多次接收。

<h2 id="4de8e4ec4db5a29659c3f35f636aa421"></h2>


# 粘包与分包解决方法

1. 采用分隔符
2. 在数据包中添加长度
    - 有个小问题就是如果客户端第一个数据包数据长度封装的有错误，那么很可能就会导致后面接收到的所有数据包都解析出错
    - 对数据长度做校验 ?



<h2 id="d12e572ac98a9695d8835393fefd717a"></h2>


# 网络设备工作在哪层？

- 只知道MAC不知道IP的算第2层，例如普通交换机
- 只知道IP不知道port（也就不管TCP还是UDP）的算第3层，例如普通路由器
- 知道IP还知道port的算第4层，例如 NAT


<h2 id="4a59949b01af57900c573cc1a578b8c3"></h2>


# 分片传输数据 

- 假设MTU(IP层) 为1500, IP头部20字节，所以超过1480字节的数据包，会被分片传输
- UDP传输2000字节数据，UDP头8字节，IP 协议 payload = 2008
    - IP 会把数据包拆分成
        1. 20(IP header) + 8(UDP协议头) + 1472 payload
        2. 20(IP header) + 528 payload   
- TCP传输2000字节
    - TCP引入 MSS = MTU-40 = 1460
    - TCP会根据MSS 把数据拆分为两个数据段
        1. 20 IP header + 20 TCP header + 1460 payload
        2. 20 IP header + 20 TCP header + 540 payload

- 总结
    - IP 协议拆分数据是因为物理设备的限制，一次能够传输的数据由路径上 MTU 最小的设备决定，一旦 IP 协议传输的数据包超过 MTU 的限制就会发生丢包，所以我们需要通过路径 MTU 发现获取传输路径上的 MTU 限制；
    - TCP 协议拆分数据是为了保证传输的可靠性和顺序，作为可靠的传输协议，为了保证数据的传输顺序，它需要为每一个数据段增加包含序列号的 TCP 协议头，如果数据段大小超过了 IP 协议的 MTU 限制， 就会带来更多额外的重传和重组开销，影响性能。
    - UDP 协议的数据报不应该超过 MTU - 28 字节，一旦超过该限制，IP 协议的分片机制会增加 UDP 数据报无法重组的可能性

<h2 id="8f4f3bfa233c4b26a13483f4f73860e0"></h2>


# Difference of Hub / Switch / Router 


device | usage | network
--- | ---  | --- 
hub/switch | used to create networks | LAN-WAN 
router | used to connect networks  | WAN-WAN


- Hubs and Switches are used to exchange data within a local area network.
    - Not used to exchange data outside their own network.
- To exchange data outside their own network, a device needs to be able to read IP address.
- Router cat route or forward data from 1 network to another based on their IP address. 
    - When a data packet is received from the router,  the router inspects the packet's IP and determines if the packet was meant for its own network or if it's meant for another network. 
    - A router is essentially the Gateway of a network. 


device | how to work | network
--- | ---  | --- 
Hub | only detects that a devices is physically connected to it
Switch | cat detect specific devices that are connected to it.  keeps a record ot the MAC 

- Hub
    - 把几台机器，连接到局域网内，  任意时刻有 packet 到达hub端口，hub只会简单的复制它 并转发到所有的端口。
- Switch
    - can learn the physical addresses （MAC) of the devices connected to it, and stores these physical addresses.'
    - when a data packet is sent to a switch, it's only directed to the intended destination port, unlike a hub where hub will just broadcast data packet to every port.

