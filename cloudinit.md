

cloud-init 服务， 要求： 在network 之前启动 


A   要求B : B 在A 之前启动， 

在A 配置文件添加

Requires=B 和 After=B

就是说： 在后启动的配置文件中 写这个依赖关系。



典型的情况是，单元A要求单元B在A启动之前运行。在此情况下，向单元A配置文件中的 [Unit] 段添加 Requires=B 和 After=B 即可



这样子：  要修改 network 服务





为满足 灵活和容错，systemd 提供了多种依赖类型：
Requires: 严格依赖。如果被依赖的 单元 启动失败，依赖它 的单元也不会被启动。
Wants：在启动一个 单元的时候，systemd 会去启动 它的 wants 单元。但是不管 wants 单元有没有启动成功，该单元都会被启动。
Requisite：我们在 启动 拥有requisite依赖 的单元的时候，会先去看 这个requisite 依赖 有没有 起起来，如果没有，该单元也起不起来。
Conflicts：当 启动一个 拥有conflict依赖的单元，如果那个 依赖单元 是 已启动的，systemd 会去把它关掉。同时启动 冲突单元 的话，都会失败。
————————————————
版权声明：本文为CSDN博主「Code  speaking」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/greywolf5/article/details/120738968


查看某个单元的依赖：
# systemctl -p wants unit

次序

require 或者 wants 可能 会让 多个单元同时启动。有时我们需要先后启动。
使用以下标记：
before: 在 before 单元 之前启动
After：在 After 单元 之后启动
当你使用排序，systemd 会等待 被依赖的单元 变成 已启动，才启动依赖的单元。
 
[systemd 中的requires, wants, before, after](https://www.cnblogs.com/longchang/p/10985352.html)

这个待实验。https://www.cnblogs.com/longchang/p/10985352.html


[root@pcentos system]# cat cloud-init.service
[Unit]
Description=Initial cloud-init job (metadata service crawler)
Wants=cloud-init-local.service
Wants=sshd-keygen.service
Wants=sshd.service
After=cloud-init-local.service
After=systemd-networkd-wait-online.service
After=network.service
After=NetworkManager.service
Before=network-online.target
Before=sshd-keygen.service
Before=sshd.service
Before=systemd-user-sessions.service
ConditionPathExists=!/etc/cloud/cloud-init.disabled
ConditionKernelCommandLine=!cloud-init=disabled

[Service]
Type=oneshot
ExecStart=/usr/bin/cloud-init init
RemainAfterExit=yes
TimeoutSec=0

# Output needs to appear in instance console output
StandardOutput=journal+console

[Install]
WantedBy=cloud-init.target

cloud-init是在network.service 之后启动，但这个不是严格的先后依赖顺序， 所以社区这种配置满足不了我们。 

1.新建一个服务A，配置为强依赖关系：服务A在network和NetworkManager服务之前启动, 如:

在systemd配置文件中，修改network和NetworkManager服务的systemd service文件，在其中加入
Requires=A  
After=A 

2.服务A的逻辑是： 检查ifcfg-eth0文件，如果其内容不是dhcp, 则修改为dhcp。 






## 两种方法打开qcow2文件

https://juejin.cn/post/7081869715701760030

1. qemu-nbd ->失败
2. libguestfs,guestmount ->成功

可以通过-i参数替代-m来实现自动挂载有效分区



## issue 1:使用qemu-nbd 打不开qcow2文件

[root@pcentos kvm-image]# qemu-nbd -c /dev/nbd0  openEuler.qcow2 
[root@pcentos kvm-image]# ls -alh /dev/nbd0*
brw-rw----. 1 root disk 43, 0 Jan  3 10:30 /dev/nbd0
brw-rw----. 1 root disk 43, 1 Jan  3 10:30 /dev/nbd0p1
[root@pcentos kvm-image]# mount /dev/nbd0p1 /media/usb0
mount: wrong fs type, bad option, bad superblock on /dev/nbd0p1,
       missing codepage or helper program, or other error

       In some cases useful info is found in syslog - try
       dmesg | tail or so.


Disk /dev/nbd0: 107.4 GB, 107374182400 bytes, 104857600 sectors
Units = sectors of 1 * 1024 = 1024 bytes
Sector size (logical/physical): 1024 bytes / 1024 bytes
I/O size (minimum/optimal): 1024 bytes / 1024 bytes
Disk label type: dos
Disk identifier: 0x82fc2ea6

     Device Boot      Start         End      Blocks   Id  System
/dev/nbd0p1   *        2048   209715199   209713152   83  Linux

[root@pcentos kvm-image]# mount /dev/nbd0p1 /media/usb0
mount: wrong fs type, bad option, bad superblock on /dev/nbd0p1,
       missing codepage or helper program, or other error

       In some cases useful info is found in syslog - try
       dmesg | tail or so.

[ 8634.861795]  nbd0: p1
[ 8634.861853] nbd0: p1 size 419426304 extends beyond EOD, truncated