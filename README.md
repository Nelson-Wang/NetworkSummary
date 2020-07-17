# NetworkSummary
# ARP工作原理
一个局域网过程：

1>   当主机A向本局域网上的某个主机B发送IP数据报时，就先在自己的ARP缓冲表中查看有无主机B的IP地址。

2>   如果有，就可以查出其对应的硬件地址，再将此硬件地址写入MAC帧，然后通过以太网将数据包发送到目的主机中。

3>   如果查不到主机B的IP地址的表项。可能是主机B才入网，也可能是主机A刚刚加电。其高速缓冲表还是空的。在这中情况下，主机A就自动运行ARP。
    
   (1)ARP进程在本局域网上广播一个ARP请求分组。ARP请求分组的主要内容是表明：我的IP地址是192.168.0.2，我的硬件地址是00-00-C0-15-AD-18.我想知道IP地址为192.168.0.4的主机的硬件地址。
   
   (2)在本局域网上的所有主机上运行的ARP进行都收到此ARP请求分组。
   
   (3)主机B在ARP请求分组中见到自己的IP地址，就向主机A发送ARP响应分组，并写入自己的硬件地址。其余的所有主机都不理睬这个ARP请求分组。ARP响应分组的主要内容是表明：“我的IP地址是                   192.168.0.4,我的硬件地址是08-00-2B-00-EE-AA”,请注意：虽然ARP请求分组是广播发送的，但ARP响应分组是普通的单播，即从一个源地址发送到一个目的地址。
   
   (4)主机A收到主机B的ARP响应分组后，就在其ARP高速缓冲表中写入主机B的IP地址到硬件地址的映射。
   

目标设备和源主机不在同一个局域网：

1>此时主机A就无法解析出主机B的硬件地址（实际上主机A也不需要知道远程主机B的硬件地址）;

2>此时主机A需要的是将路由器R1的IP地址解析出来，然后将该IP数据报发送给路由器R1.

3>R1从路由表中找出下一跳路由器R2，同时使用ARP解析出R2的硬件地址。于是IP数据报按照路由器R2的硬件地址转发到路由器R2。

4>路由器R2在转发这个IP数据报时用类似方法解析出目的主机B的硬件地址，使IP数据报最终交付给主机B.

# 补充说明
ARP缓存：这个缓存的实现是ARP实现的核心

ARP缓存是个用来储存IP地址和MAC地址的缓冲区，其本质就是一个IP地址-->MAC地址的对应表，表中每一个条目分别记录了网络上其他主机的IP地址和对应的MAC地址。每一个以太网或令牌环网络适配器都有自己单独的表。当地址解析协议被询问一个已知IP地址节点的MAC地址时，先在ARP缓存中查看，若存在，就直接返回与之对应的MAC地址，若不存在，才发送 ARP请求向局域网查询。
为使广播量最小，ARP维护IP地址到MAC地址映射的缓存以便将来使用。ARP缓存可以包含动态和静态项目。动态项目随时间推移自动添加和删除。每个动态ARP缓存项的潜在生命周期是10分钟。新加到缓存中的项目带有时间戳，如果某个项目添加后2分钟内没有再使用，则此项目过期并从ARP缓存中删除；如果某个项目已在使用，则又收到2分钟的生命周期；如果某个项目始终在使用，则会另外收到2分钟的生命周期，一直到10分钟的最长生命周期。静态项目一直保留在缓存中，直到重新启动计算机为止。

# 总结
ARP，就是通过IP，获取到mac，中间可能会中转多次，最终会获取到初始目的的IP，ARP的请求和应答都还是在局域网中。ARP的实现关键在于ARP缓存。

# 免费ARP
免费ARP报文是一种特殊的ARP报文，该报文中携带的发送端IP地址和目标IP地址都是本机IP地址，报文源MAC地址是本机MAC地址，报文的目的MAC地址是广播地址。  

设备通过对外发送免费ARP报文来实现以下功能：  
  
1>  确定其它设备的IP地址是否与本机的IP地址冲突。当其它设备收到免费ARP报文后，如果发现报文中的IP地址和自己的IP地址相同，则给发送免费ARP报文的设备返回一个ARP应答，告知该设备IP地址冲突。
  
2>  设备改变了硬件地址，通过发送免费ARP报文通知其它设备更新ARP表项。  
 

