# NetWork

* 硬件网卡，网线，交换机这些，用来处理数据的。
* 协议数据在网络中通信如何组织？如何识别？如何保证数据的正确性
* 操作系统这就是如何把计算机网络和操作系统结合起来的问题了
    - 对于操作系统来说，网卡也是一种硬件资源。但是网络不单只是一种硬件，而是一种媒体入口。比如操作系统管理硬盘，当然不是简单的记一下硬盘有多大，然后一切操作都交给硬盘芯片去做，更多的需要组织硬盘的扇区，分区，记录文件和扇区/偏移的关系等等。
    - 操作系统对于网络来说也是如此，要记录自身在网络的标识（ip），可被他人访问的入口（port），以及对方的信息（remote ip/port）。连接，断开，数据确认等操作也是由协议控制。传递自身消息给对方，类似访问硬盘一样把内存中的数据传递给网卡缓存，再发消息给网卡让网卡去传数据，而是否发送成功这些保证不再由硬件中断信号反馈，而是通过网络协议完成。接收对方消息，也是接收到网卡中断，再把数据从网卡缓存移动到内存中，再通过协议给予对方反馈。

## 网络

* 集线器（Hub）：一个将网线集结起来的作用，实现最初级的网络互通。集线器是通过网线直接传送数据的，工作在物理层
* 交换机：给这台设备加入一个指令，可以根据网口名称自动寻址传输数据。实现了任意两台电脑间的互联。工作在数据链路层。
    - 保存的是每个计算机的网卡MAC地址与你所在的计算机的接口：通过学习，可以把MAC地址，端口号完善
    - 既可以连接PC机，又可以连接路由器
    - 根据Mac地址表转发数据
    - mac地址表含有Mac地址和接口
* 路由器：先在各自的操作系统上加上一套相同的协议。不同村落通信时，信息经协议加工成统一形式，再经由一个特殊的设备传送出去。这个设备就叫做路由器。每个机器都被赋予了一个IP地址。协议便是TCP/IP协议簇。
    - 是用来互联不同网段的设备。根据路由表转发数据
    - 理由表中含有网段和接口（！！！注意：绝对不能把路由器接在两个相同的网段上。）
* ARP协议：它是一种广播，通过ip地址得到MAC地址的广播协议。
    - 代理ARP工作原理：路由器可以分割网段和广播，比如广播后只能在同一个网段接收到，而在其他的网段不会听见广播。

```
arp -a
```

## OSI（Open Systems Interconnection Model）

* 从上往下的，越底层越接近硬件，越往上越接近软件，是一个标准
* 计算机与网络传输：每层进行层层解包和附加自己所要传递的信息，术语叫做报头。
* OSI（Open Systems Interconnection Model）
    - 应用层：HTTP,解决如何包装数据。各种应用软件，包括 Web 应用。利用TCP在两台电脑(通常是Web服务器和客户端)之间传输信息的协议。
    - 表示层：数据格式标识，基本压缩加密功能。上传的数据是以什么样的编码来编码，编码状态和回话方式，不是以人来自定义来完成的，而是由人在应用层操作来完成的
    - 会话层：控制应用程序之间会话能力；如不同软件数据分发给不同软件。_软件_
    - 传输层：端到端传输数据的基本功能；如 TCP、UDP。在原有的上层的数据外围标记两个标签：第一源端口，第二目的端口，从这个端口出去，访问另一个端口  数据被称作段（Segments）
        + TCP（Transmission Control Protocol）传输控制协议
    - 网络层：定义IP编址，定义路由功能；如不同设备的数据转发。 数据被称做包（Packages）ip地址层 网络层+ip 地址层 在数据外围加一个原ip地址和目的ip地址
        + IP（Internet Protocol）互联网协议
            * 解决三个问题：寻址、路由、封装；
            * 分为两部分：网络地址+主机地址
            * 子网掩码：TCP/IP协议使用子网掩码确定主机是在本地子网中还是在远程网络中。将Ip地址和子网掩码排在一起比较，就可以分清楚改地址的网络部分和主机部分
                - A类IP地址: 0.0.0.0~127.0.0.0
                - B类IP地址:128.0.0.1~191.255.0.0
                - C类IP地址:192.168.0.0~239.255.255.0
            * IP协议头
                - 八位的TTL字段:规定该数据包在穿过多少个路由之后才会被抛弃。某个IP数据包每穿过一个路由器，该数据包的TTL数值就会减少1，当该数据包的TTL成为零，它就会被自动抛弃。
        + ARP及RARP协议:根据IP地址获取MAC地址的一种协议
            * 一种解析协议，本来主机是完全不知道这个IP对应的是哪个主机的哪个接口，当主机要发送一个IP包的时候，会首先查一下自己的ARP高速缓存（就是一个IP-MAC地址对应表缓存）
            * 如果查询的IP－MAC值对不存在，那么主机就向网络发送一个ARP协议广播包，这个广播包里面就有待查询的IP地址，而直接收到这份广播的包的所有主机都会查询自己的IP地址，如果收到广播包的某一个主机发现自己符合条件，那么就准备好一个包含自己的MAC地址的ARP包传送给发送ARP广播的主机。
            * 广播主机拿到ARP包后会更新自己的ARP缓存（就是存放IP-MAC对应表的地方）。发送广播的主机就会用新的ARP缓存数据准备好数据链路层的的数据包发送工作
        + 
    - 数据链路层：定义数据的基本格式，如何传输，如何标识；如网卡MAC地址。责将0、1序列划分为数据帧从一个节点传输到临近的另一个节点,这些节点是通过MAC来唯一标识的(MAC,物理地址，一个主机会有一个MAC地址)。数据被称为帧（Frames） _操作系统_
        + MAC地址：介质访问控制（Media Access Control）
        + 完成加封（盖个戳，加个标记）与解封、数据量层:加封：盖个戳，加个标记， 数据链路层重点是在数据包外部加一个原MAC地址，目标Mac地址的这么一个标记。mac地址：网卡的物理地址，也是网卡的实际地址。 加原Mac地址+目的mac地址。mac地址不能改变，在电脑出厂时已经刷在了网卡上了，Mac地址犹如身份证的ID是唯一的。
        + 封装成帧: 把网络层数据报加头和尾，封装成帧,帧头中包括源MAC地址和目的MAC地址。
        + 透明传输:零比特填充、转义字符。
        + 可靠传输: 在出错率很低的链路上很少用，但是无线链路WLAN会保证可靠传输。
        + 差错检测(CRC):接收者检测错误,如果发现差错，丢弃该帧。
    - 物理层：数据被称为比特流（Bits）负责0、1比特流与物理设备电压高低、光的闪灭之间的互换。通信线缆（光缆、无线），线缆的标准统统属于物理层  _物理设备_
* TCP/IP协议模型（Transmission Control Protocol/Internet Protocol）层对应关系
    - 应用层：HTTP 应用层 表示层 会话层 curl
        + 在传输数据时，可以只使用（传输层）TCP/IP协议，但是那样的话，如果没有应用层，便无法识别数据内容，如果想要使传输的数据有意义，则必须使用到应用层协议，应用层协议有很多，比如HTTP、FTP、TELNET等，也可以自己定义应用层协议。WEB使用HTTP协议作应用层协议，以封装HTTP文本信息，然后使用TCP/IP做传输层协议将它发到网络上。
    - 传输层  TCP UDP telent
    - 网络层 IP ping traceroute 
    - 数据链路层 物理层

![七层协议](../_static/osi_1.png "Optional title")
![数据流](../_static/osi2.jpeg "Optional title")

```
10.10.27.115 # ip
255.255.255.0 # 子网掩码
10.10.27.0 # 网络
0.0.0.115 # 主机地址为
```

## 网络命令

* ping:确定网络是否正确连接，以及网络连接的状况.是ICMP的最著名的应用，是TCP/IP协议的一部分。利用"ping"命令可以检查网络是否连通，可以很好地帮助我们分析和判定网络故障。原理是用类型码为0的ICMP发请 求，受到请求的主机则用类型码为8的ICMP回应。
* ipconfig:用于显示当前的TCP/IP配置的设置值
* Traceroute:是用来侦测主机到目的主机之间所经路由情况的重要工具。它收到到目的主机的IP后，首先给目的主机发送一个TTL=1的UDP数据包，而经过的第一个路由器收到这个数据包以后，就自动把TTL减1，而TTL变为0以后，路由器就把这个包给抛弃了，并同时产生 一个主机不可达的ICMP数据报给主机。主机收到这个数据报以后再发一个TTL=2的UDP数据报给目的主机，然后刺激第二个路由器给主机发ICMP数据 报。如此往复直到到达目的主机。这样，traceroute就拿到了所有的路由器IP。 -

```sh
ping  主机名
ping  域名
ping  IP地址

ping 127.0.0.1 # 如果测试成功，表明网卡、TCP/IP协议的安装、IP地址、子网掩码的设置正常。如果测试不成功，就表示TCP/IP的安装或设置存在有问题。
ping 本机IP地址 # 如果测试不成功，则表示本地配置或安装存在问题，应当对网络设备和通讯介质进行测试、检查并排除。
ping 局域网内其他IP #如果测试成功，表明本地网络中的网卡和载体运行正确。但如果收到0个回送应答，那么表示子网掩码不正确或网卡配置错误或电缆系统有问题。
ping 网关IP # 这个命令如果应答正确，表示局域网中的网关路由器正在运行并能够做出应答。
ping 远程IP # 如果收到正确应答，表示成功的使用了缺省网关。对于拨号上网用户则表示能够成功的访问Internet（但不排除ISP的DNS会有问题）。
ping localhost # local host是系统的网络保留名，它是127.0.0.1的别名，每台计算机都应该能够将该名字转换成该地址。否则，则表示主机文件（/Windows/host）中存在问题。
ping www.yahoo.com # 对此域名执行Ping命令，计算机必须先将域名转换成IP地址，通常是通过DNS服务器。如果这里出现故障，则表示本机DNS服务器的IP地址配置不正确，或它所访问的DNS服务器有故障如果上面所列出的所有ping命令都能正常运行，那么计算机进行本地和远程通信基本上就没有问题了。但是，这些命令的成功并不表示你所有的网络配置都没有问题，例如，某些子网掩码错误就可能无法用这些方法检测到。

ping IP -t # 连续对IP地址执行ping命令，直到被用户以Ctrl+C中断。
ping IP -l 2000 # 指定ping命令中的特定数据长度（此处为2000字节），而不是缺省的32字节。
ping IP -n 20 # 执行特定次数（此处是20）的ping命令。

ipconfig # 显示每个已经配置了的接口的IP地址、子网掩码和缺省网关值
ipconfig /all # 为DNS和WINS服务器显示它已配置且所有使用的附加信息，并且能够显示内置于本地网卡中的物理地址（MAC）
```

## 网络模型

* epoll

## 工具

* [localtunnel/localtunnel](https://github.com/localtunnel/localtunnel):expose yourself https://localtunnel.me
* [cisco/joy](https://github.com/cisco/joy):A package for capturing and analyzing network flow data and intraflow data, for network research, forensics, and security monitoring.
* [wireshark](https://www.wireshark.org)
* [SolarWinds](http://www.solarwinds.com):管理大小企业网络上的网络流量。网络设备监控器可监控你网络上的任何一个设备，查找各种提示或错误
* [maxmcd/webtty](https://github.com/maxmcd/webtty):Share a terminal session over WebRTC https://maxmcd.github.io/webtty/
* [fatedier/frp](https://github.com/fatedier/frp):A fast reverse proxy to help you expose a local server behind a NAT or firewall to the internet.
* [v2ray/v2ray-core](https://github.com/v2ray/v2ray-core):A platform for building proxies to bypass network restrictions. https://www.v2ray.com/
* [librenms/librenms](https://github.com/librenms/librenms):Community-based GPL-licensed network monitoring system http://www.librenms.org/

## 参考

* [SystemsApproach/book](https://github.com/SystemsApproach/book):Meta-data and Makefile needed to build the book. Main starting point.
* [TCP/IP 视频讲解 计算机网络](https://www.bilibili.com/video/av10610680)
