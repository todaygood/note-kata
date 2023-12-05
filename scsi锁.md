# scsi锁 

https://my.oschina.net/LastRitter/blog/1541364
https://my.oschina.net/LastRitter/blog/1541361


## scsi锁 

https://blog.csdn.net/hughwang1216/article/details/46333735


oracle rac IO 隔离的存储SCSI锁原理



闭眼忘世界

于 2015-06-02 19:36:01 发布

1733
 收藏
分类专栏： Oracle RAC 文章标签： oracle rac SCSI锁原理

Oracle RAC
专栏收录该内容
5 篇文章0 订阅
订阅专栏
SCSI锁的作用:

在一个共享存储的环境下，多台前端主机可能会同时访问同一台存储设备，如果此时多台主机在同一时点上对一个LUN进行写操作，那么可想而知这个LUN将不知道哪个数据先写，哪个数据后写。为了防止这种情况发生而导致的数据损坏，所以就有了SCSI锁的概念。通过SCSI Reservation机制来进行SCSI锁的操作，目前绝大多数的磁盘都支持‘SCSI reservvation命令‘。如果一台主机给磁盘传输了一条SCSI Reservation命令，则这个磁盘对于其他的主机就处于锁定状态。如果有其他的主机给已经被锁定的磁盘发送读写请求，则会收到‘reservation conflict’报错信息。如果保留SCSI锁的主机崩溃，或者其他主机给磁盘发送‘break reservation或者reset target命令，用来解除SCSI锁。然后，第二个主机发送I/O请求之前需要重新发送SCSI Reservation命令给磁盘。

SCSI锁的分类：

SCSI锁有两种类型：SCSI-2 Reservation和SCSI-3 Reservation。一个LUN上只能存在一种类型的SCSI锁。

SCSI-2 Reservation只允许设备被发出SCSI锁的Initiator访问，也就是主机的HBA。比如主机1上的HBA1对访问的LUN加上SCSI-2锁，此时即使主机1的HBA2也无法访问这个LUN。所以SCSI-2 Reservation也被称为Single Path Reservation。

SCSI-3 Reservation(Persistent Reservation)是使用PR Key来对磁盘进行加锁。通常一台Host会有唯一的PR Key，不同的主机对应的PR Key也不同。所以一般SCSI-3 Reservation通常被应用在多通路的共享环境下面。这里SCSI-3 Reservation也称之为Persistent Reservation。

Windows Cluster中的SCSI锁：

Windows 2003集群中使用SCSI-2 reserve/release命令。作为非持久的reservation，所以集群中的一台节点会持有SCSI-2 Reservation的锁，然后每过3秒会重新刷新一次。如果故障转移发生，则切换节点主机会在相应的磁盘上放置SCSI-2 Reservation然后维护SCSI锁。如果所有节点主机上的集群服务都会关闭，Reservation也不会保留。


## 锁的问题 

https://blog.csdn.net/weixin_42051187/article/details/121078065

[华为云scsi共享盘](https://support.huaweicloud.com/productdesc-evs/zh-cn_topic_0052554220.html)

https://support.huaweicloud.com/productdesc-evs/zh-cn_topic_0032860759.html

librbd I/O的协议栈  https://blog.csdn.net/u010317005/article/details/79264751

[共享scsi盘方案](https://zhuanlan.zhihu.com/p/150696816)