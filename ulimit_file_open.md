
## ulimit的管理的维度

虽然ulimit的u是user的意思，但事实证明，ulimit控制的维度是shell会话或shell创建的进程（至少对于nofile来说）。即：当前用户打开的文件数，是可以远远超过nofile的值。|

所以，通过lsof | wc -l 查看系统打开文件数，来判断是否打开文件数是否超了，是不正确的。另外，lsof  wc -l 是也并不反映系统打开的文件数！（后续周刊补充）

https://imshuai.com/too-many-open-files-ulimit


https://jackxiang.com/post/6570/



1）nofile 
该值是指单进程的最大打开文件数。

2）nr_open 
该值是指单个进程可分配的最大文件数，通常默认值为1024*1024=1048576。

3）file-max 
该值是系统内核一共可以打开的最大值，默认值是185745。

https://help.fanruan.com/finereport/doc-view-4086.html

各种命令，参见：https://www.jianshu.com/p/fbfd52ff8e9e


# 解决动态修改/proc/{pid}/limits

cat /proc/39977/limits 
Limit                     Soft Limit           Hard Limit           Units     
Max cpu time              unlimited            unlimited            seconds   
Max file size             unlimited            unlimited            bytes     
Max data size             unlimited            unlimited            bytes     
Max stack size            8388608              unlimited            bytes     
Max core file size        0                    unlimited            bytes     
Max resident set          unlimited            unlimited            bytes     
Max processes             65535                65535                processes 
Max open files            1024                 4096                 files     
Max locked memory         65536                65536                bytes     
Max address space         unlimited            unlimited            bytes     
Max file locks            unlimited            unlimited            locks     
Max pending signals       514823               514823               signals   
Max msgqueue size         819200               819200               bytes     
Max nice priority         0                    0                    
Max realtime priority     0                    0                    
Max realtime timeout      unlimited            unlimited            us 

上面的展示是查看某个进程的打开文件的相关参数最大值，其中，Max open files 是打开文件的最大数量，那么如何动态修改这个值呢？ 毕竟1024太小了，操作如下：

# CentOS7系统使用命令
prlimit --nofile=65536:65536 --pid 39977


当然，可以通过重启这个进程所对应的服务（如果可重启，也不会来修改这个session里的参数了）来重新读取系统里的默认设置。
————————————————
版权声明：本文为CSDN博主「空气中的臭氧」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/dqchouyang/article/details/115230076


怎么修改？
# CentOS7系统使用命令
prlimit --nofile=65536:65536 --pid 39977

对应内核代码分析：https://blog.csdn.net/Rong_Toa/article/details/115308673

# 永久解决
vim /etc/security/limits.conf
# 添加如下的行
* soft nproc 65536
* hard nproc 65536
* soft nofile 65536
* hard nofile 65536


（1）查看Linux系统默认的最大文件句柄数

（2）查看当前进程打开了多少句柄数

（3）可以根据ID号来查看进程名。

## 现在的问题是： limits.conf 中的nofile 和 ulimit -n 不一样，nofile 是655360 ， 而ulimit -n 却是65536 


 
## How to set limits for services in RHEL 7 and systemd

https://access.redhat.com/solutions/1257953


# mkdir -p /etc/systemd/system/tftp.service.d/
# cat >/etc/systemd/system/tftp.service.d/filelimit.conf <<EOF
[Service]
LimitNOFILE=500000
EOF



刨根问底儿，看我如何处理 Too many open files 错误！https://blog.csdn.net/zhangyanfei01/article/details/113765199

docker问题，知乎这个比较深入： https://zhuanlan.zhihu.com/p/558527869


k8s排错---Accept error: accept unix /var/run/docker.sock: accept4: too many open files； retrying in 5m
https://blog.csdn.net/CN_LiTianpeng/article/details/118513640



1、办法一：适用场景 – 单个进程打开文件句柄数过多

ulimit中的nofile表示单进程可以打开的最大文件句柄数，可以通过ulimit -a查看，子进程默认继承父进程的限制（注意，是继承，不是共享，子进程和父进程打开的文件句柄数是单独算的）。

网上还有一种解读是nofile表示单用户可以打开的文件句柄数，因为他们在limit.conf中看到类似于“openstack soft nofile 65536”，便认为是openstack用户最多可以打开的文件句柄数。该解读是错误的，“openstack soft nofile 65536”表示的含义是当你执行"su - openstack"切换到openstack用户后，你创建的所有进程最大可以打开的文件句柄数是65536。

要查看一个进程可以打开的文件句柄数，可以通过“cat /proc//limits”查看。

要修改ulimit中的nofile，可以通过修改/etc/security/limits.conf文件，在其中加入类似“openstack soft nofile 65536”的语句来进行修改。修改完成后，可以通过“su - openstack”切换用户，或者重新登录，来使该配置生效。

要动态修改一个进程的限制，可以使用prlimit命令，具体用法为：“prlimit --pid ${pid} --nofile=102400:102400”。
————————————————
版权声明：本文为CSDN博主「知无涯学无尽」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/CN_LiTianpeng/article/details/118513640


https://github.com/awslabs/amazon-eks-ami/issues/278

Linux kernel put nofile under cgroup control, and they are kind of independent. We don't necessarily need to change host ulimit


docker nofile 配置说明
https://www.jianshu.com/p/e635c13916d3


## 淘宝这把

http://blog.yufeng.info/archives/tag/ulimit


深入理解Docker ulimit http://dockone.io/article/522

https://github.com/moby/moby/issues/37061 这个例子比较契合我们的问题。 

https://cloud.tencent.com/developer/article/1886443 里面提到docker 默认的nofile 是65536

docker 18.09.9 默认的nofile 就是65536 , 待确认。 

