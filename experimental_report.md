# 计算机网络实验报告  

---

## 目录  
- [验证性实验](#验证性实验)  
  - [ipconfig](#ipconfig)  
  - [ping](#ping)  
  - [tracert](#tracert)
  - [DHCP](#DHCP)
  - [netstat](#netstat)
  - [DNS](#DNS)
  - [cache](#cache)
- [Wireshark 实验](#Wireshark 实验)

  



## 验证性实验  
   
### ipconfig
`ipconfig`是微软操作系统的计算机上用来控制网络连接的一个命令行工具。它的主要用来显示当前网络连接的配置信息（`/all` 参数）。
#### 实作一  
使用 `ipconfig/all` 查看自己计算机的网络配置，尽可能明白每行的意思，特别注意 **IP** 地址、子网掩码 **Subnet Mask**、网关 **Gateway**。  

![自己的计算机](./pictures/p1.png)  

![IP网关](./pictures/p2.png)  
- IPv4 地址:`192.168.31.1`就是本机所分配到的IP地址  
- 子网掩码:子网掩码是一个32位地址，用于屏蔽IP地址的一部分以区别网络标识和主机标识，并说明该IP地址是在局域网上，还是在广域网上。本机的子网掩码为:`255.255.255.0`  
- 网关:本机的默认网关是`192.168.31.1`。默认网关是子网与外网连接的设备，通常是一个路由器。它的地址通常是一个子网下的第一个地址。  
  
#### 实作二  
使用 `ipconfig/all` 查看**旁边**计算机的网络配置，看看有什么异同。  
![王亚新IP](./pictures/p3.png)  
这是在另一台计算机上运行`ipconfig/all`的结果（条件有限旁边没有其它计算机，是同学家的计算机）。  
可以看到这台计算机上的数据与本机不同，IP、子网掩码、网关都与本机**不同**，与本机不在同一子网下。  
  
***
  
### ping  
`PING` （Packet Internet Groper），因特网包探索器，用于测试网络连接量的程序 。`PING` 是工作在 `TCP/IP` 网络体系结构中应用层的一个服务命令， 主要是向特定的目的主机发送 ICMP（Internet Control Message Protocol 因特网报文控制协议）Echo 请求报文，测试目的站是否可达及了解其有关状态。  
#### 实作一  
测试本机到**抖音官网**的连通性  
`ping www.douyin.com`  
![ping抖音](./pictures/p4.png)  
抖音官网返回的数据有**字节、时间、TTL**  
- **字节**：字节=32表示抖音官网回复的数据包大小为`32个字节`  
- **时间**：表示与主机往返一次所耗时间为`6ms（5ms）`  
- **TTL**:`TTL`全称`Time To Live`，意为**生存时间**，也就是该IP包的**生存周期**。
IP包在从目标网站传回本机时，每路过一个路由器，它的TTL就会减一，当TTL为0时，会抛弃此IP包。  
#### 实作二  
使用 `ping/?` 命令了解该命令的各种选项并实际使用。  
![ping/?](./pictures/p5.png)  
- `ping -n 10 www.baidu.com`   
向百度发出请求，并让它返回**10条**回显请求。  

![ping-n](./pictures/p6.png)  

- `ping -a www.baidu.com`  
ping -a 将**地址**解析为**主机名**  

![ping-a](./pictures/p7.png)  
  
***  
  
### tracert  
`TRACERT` (Trace Route 的组合缩写)，也称为路由追踪，该命令行程序可用于跟踪 Internet 协议 （IP） 数据包传送到目标地址时经过的路径。  
#### 实作一  
了解本机到www.baidu.com中间经过了哪些节点（路由器）及其它状态,使用`tracert www.baidu.com`命令。  
![tracert baidu](./pictures/p8.png)  

从返回的数据分析：  
**最左边一列**的数字表示本机到baidu.com经历了12个节点  。
**中间三列**数字分别表示连接到**每个路由节点的速度**、**返回速度**、**多次链接反馈的平均值**。  
最右边的就是各个节点的**IP**  

在网站http://ip.cn上查看这些节点的位置。
- 192.168.31.1 内网IP 内网IP
- 192.168.0.1  内网IP 内网IP
- 192.168.1.1 内网IP 内网IP
- 10.10.0.1 内网IP 内网IP
- 218.207.38.237 中国 重庆 重庆市 移动
- 218.207.40.10 中国 重庆 重庆市 移动
- 120.241.49.26 中国 广东省 深圳市 移动
- 183.232.231.172 中国 广东省 广州市 移动  
使用`tracert www.douyin.com`跟踪抖音官网  
![tracert douyin](./pictures/p9.png)  
#### 实作二  
`ping.pe` 这个网站可以探测从全球主要的 ISP 到某站点如 https://qige.io 的线路状态，当然也包括各线路到该主机的路由情况。  
![ping.pe](./pictures/p10.png)  
> 在以上两个实作中，如果你留意路径中的节点，你会发现无论是访问百度还是棋歌教学网，路径中的第一跳都是相同的，甚至你应该发现似乎前几个节点都是相同的，你的解释是什么？  
  
我们在追踪某站点时，tracert会记录我们经过的节点，不论是访问百度还是棋歌教学网，都需要经过本机当前局域网的交换机，也就是默认网关，这样数据包才能从这里发送出去。  
> 在追踪过程中，为什么路径中某些节点显示为 * 号？  
  
*号的节点表示这些节点和我们当前使用的宽带是无法联通的，可能的原因是这些节点设置了不可ping和tracert或者做了过滤限制。  
  
***  
  
### ARP  
`ARP`（Address Resolution Protocol）即地址解析协议，是用于根据给定网络层地址即 IP 地址，查找并得到其对应的数据链路层地址即**MAC地址**的协议。  
#### 实作一  
运行 `arp -a` 命令查看当前的 arp 缓存， 请留意缓存了些什么。  
![arp-a](./pictures/p11.png)  

缓存了一下IP地址下所对应的MAC物理地址  
使用 `arp -d *` 删除全部缓存，再次查看缓存  
![arp-d](./pictures/p12.png)  
运行`arp -d *`后，本机与其他IP地址的一些物理地址的缓存记录被清除了。被清除的应该是本机最近访问到的节点的地址。
#### 实作二  
使用 `arp /?` 命令了解该命令的各种选项。
![arp/?](./pictures/p13.png)  
#### 实作三  
假设当前网关的 IP 地址是 `192.168.0.1`，MAC 地址是 `5c-d9-98-f1-89-64`，请使用 `arp -s 192.168.0.1 5c-d9-98-f1-89-64` 命令设置其为静态类型的。
![arp添加地址1](./pictures/p14.png)  
第一次输入`arp -s 192.168.0.1 5c-d9-98-f1-89-64`失败，需要以管理员身份运行。  
![管理员arp添加地址](./pictures/p15.png)  
以管理员身份运行cmder后成功，可以看到已经将192.168.0.1的物理地址添加到了arp缓存中。  
> 我们将网关或其它计算机的 arp 信息设置为静态有什么优缺点？  

配置静态ARP表项可以增加通信的安全性。静态ARP表项可以限制和指定IP地址的设备通信时只使用指定的MAC地址，此时攻击报文无法修改此表项的IP地址和MAC地址的映射关系，从而保护了本设备和指定设备间的正常通信。  
  
***  
  
### DHCP  
DHCP（Dynamic Host Configuration Protocol）即动态主机配置协议，是一个用于 IP 网络的网络协议，位于 OSI 模型的应用层，使用 UDP 协议工作，主要有两个用途：

- 用于内部网或网络服务供应商自动分配 IP 地址给用户
- 用于内部网管理员对所有电脑作中央管理  

简单的说，DHCP 可以让计算机自动获取/释放网络配置。  
    
#### 实作一  
使用 `ipconfig/release` 命令释放自动获取的网络配置，并用 `ipconfig/renew` 命令重新获取。  
![ipconfig/release](./pictures/p16.png)  
![ipconfig/renew](./pictures/p17.png)  
使用`ipconfig/renew`后本机重新获取了IP。  
  
***  
  
### netstat  
无论是使用 TCP 还是 UDP，任何一个网络服务都与特定的端口（Port Number）关联在一起。因此，每个端口都对应于某个通信协议/服务。

`netstat`（Network Statistics）是在内核中访问网络连接状态及其相关信息的命令行程序，可以显示路由表、实际的网络连接和网络接口设备的状态信息，以及与 IP、TCP、UDP 和 ICMP 协议相关的统计数据，一般用于检验本机各端口的网络服务运行状况。
  
#### 实作一  
Windows 系统将一些常用的端口与服务记录在 `C:\WINDOWS\system32\drivers\etc\services` 文件中，请查看该文件了解常用的端口号分配。  
![services](./pictures/p18.png)  
#### 实作二  
使用 `netstat -an` 命令，查看计算机当前的网络连接状况。  
![netsta-an](./pictures/p19.png)  
  
***  
  
### DNS  
`DNS`（Domain Name System）即域名系统，是互联网的一项服务。它作为将域名和 IP 地址相互映射的一个分布式数据库，能够使人更方便地访问互联网。DNS 使用 TCP 和 UDP 的 53 号端口。  
#### 实作一  
Windows 系统将一些固定的/静态的 DNS 信息记录在 `C:\WINDOWS\system32\drivers\etc\hosts` 文件中，如我们常用的 `localhost` 就对应 `127.0.0.1` 。  
![host](./pictures/p20.png)  
#### 实作二  
解析过的 **DNS** 记录将会被缓存，以利于加快解析速度。使用 `ipconfig /displaydns` 命令查看。我们也可以使用 `ipconfig /flushdns` 命令来清除所有的 DNS 缓存。  
![ipconfig /displaydns](./pictures/p21.png)  
使用`ipconfig /displaydns`后，显示了DNS解析器缓存的内容，包括了很多从本地Hosts文件预装载的记录以及由域名解析服务器解析的所有资源记录。  
![ipconfig /flushdns](./pictures/p22.png)  
#### 实作三  
使用 `nslookup qige.io` 命令，将使用默认的 DNS 服务器查询该域名。  
![nslookup qige.io](./pictures/p23.png)  
  
***  
  
### cache  
`cache` 即缓存，是 IT 领域一个重要的技术。我们此处提到的 cache 主要是浏览器缓存。

浏览器缓存是根据 HTTP 报文的缓存标识进行的，是性能优化中简单高效的一种优化方式了。一个优秀的缓存策略可以缩短网页请求资源的距离，减少延迟，并且由于缓存文件可以重复利用，还可以减少带宽，降低网络负荷。  
#### 实作一  
打开 Chrome 或 Firefox 浏览器，访问 https://qige.io ，接下来敲 F12 键 或 Ctrl + Shift + I 组合键打开开发者工具，选择 Network 面板后刷新页面。  
![qige.io](./pictures/p24.png)  
可以看到加载该页面花费了296ms。  
在该页面显示的列表下，看到Size列下有`memory cache`和`disk cache`两种字样。  
memory cache 就是内存中的缓存，读取速度很快，但是只要我们关闭了页面，内存中的缓存就被释放了。  
disk cache 就是存储在硬盘中的缓存，disk cache生存的时间会比memory cache更长。  
#### 实作二  
接下来仍在 Network 面板，选择 `Disable cache` 选项框，表明当前不使用 cache，页面数据全部来自于 Internet，刷新页面，再次在开发者工具底部查看加载该页面花费的时间。  
![disable cache](./pictures/p25.png)  
在选择 `Disable cache` 后重新刷新了页面，这次加载这个页面所用的时间变为了1.47s，明显比使用缓存时所花费的时间要多。  
  
***  
***  
  
## Wireshark 实验
