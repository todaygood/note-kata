
# /tmp下的文件会自动删除，原理是什么？

## centos7 之前的系统

https://blog.csdn.net/chao199512/article/details/113920887

/etc/cron.daily/下有脚本调用tmpwatch 命令，来删除文件。 


## 使用systemd的系统

https://blog.csdn.net/whatday/article/details/121428361





/tmp目录的清理规则主要取决于/usr/lib/tmpfiles.d/tmp.conf文件的设定，默认的配置内容为：

#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.
 
# See tmpfiles.d(5) for details
 
# Clear tmp directories separately, to make them easier to override
v /tmp 1777 root root 10d
v /var/tmp 1777 root root 30d
 
# Exclude namespace mountpoints created with PrivateTmp=yes
x /tmp/systemd-private-%b-*
X /tmp/systemd-private-%b-*/tmp
x /var/tmp/systemd-private-%b-*
X /var/tmp/systemd-private-%b-*/tmp
/tmp 目录默认是10天清理一次

/var/tmp 目录默认是30天清理一次

如你不想让系统自动清理/tmp下以tomcat开头的目录，那么增加下面这条内容到配置文件中即可：

x /tmp/tomcat.*