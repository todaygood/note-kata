## port bind 

[root@pcentos server]# tailf eagle_server.log 
2022-11-10 18:27:43,023 - INFO - Added job "task_manage_cron_task_for_all_big_periods_for_all_users" to job store "default"
2022-11-10 18:27:43,024 - INFO - Added job "task_del_cron_task_for_small_big_periods_for_all_users" to job store "default"
2022-11-10 18:27:43,024 - INFO - Scheduler started
 * Serving Flask app 'eagle_server' (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: on
Address already in use
Port 5000 is in use by another program. Either identify and stop that program, or start the server with a different port.
^C
[root@pcentos server]# 
[root@pcentos server]# 
[root@pcentos server]# netstat -anlp |grep 5000 
tcp        0      0 0.0.0.0:5000            0.0.0.0:*               LISTEN      4046/python         
[root@pcentos server]# supervisorctl stop 
Error: stop requires a process name
stop <name>             Stop a process
stop <gname>:*          Stop all processes in a group
stop <name> <name>      Stop multiple processes or groups
stop all                Stop all processes
[root@pcentos server]# supervisorctl stop eagleServer
eagleServer: stopped
[root@pcentos server]# supervisorctl status 
eagleServer                      STOPPED   Nov 10 06:28 PM
stockRadar                       FATAL     Exited too quickly (process log may have details)
[root@pcentos server]# netstat -anlp |grep 5000 
tcp        0      0 0.0.0.0:5000            0.0.0.0:*               LISTEN      4046/python         
[root@pcentos server]# 
[root@pcentos server]# 
[root@pcentos server]# ps aux |grep -i 4046 
root      4046  0.2  2.1 1235812 86268 ?       Sl   11:36   0:59 python -u /root/eagleeye/sdist/server/eagle_server.py
root     29053  0.0  0.0 112828  2196 pts/1    S+   18:29   0:00 grep --color=auto -i 4046
[root@pcentos server]# kill -9 4046 
[root@pcentos server]# 
[root@pcentos server]# netstat -anlp |grep 5000 
[root@pcentos server]# 
[root@pcentos server]# supervisorctl start eagleServer 
eagleServer: started
[root@pcentos server]# 
[root@pcentos server]# 
[root@pcentos server]# netstat -anlp |grep 5000 
tcp        0      0 0.0.0.0:5000            0.0.0.0:*               LISTEN      29092/python      


##  重复告警 


如果在一分钟之内做不完那么多股票的检测告警 ， 出现同一时间多次告警？ 



## 问题 ：


(venv) PS C:\Users\HuJun\Documents\down_wechat_articles> git diff .\work.json
diff --git a/work.json b/work.json
index a6fc779..febea5c 100644
--- a/work.json
+++ b/work.json
@@ -1,7 +1,7 @@
 {
   "watchlist":  [
-    {"nickname_list": ["进击的Coder","Python爬虫与数据挖掘","Python绿色通道","Python开发者"],"tag": "Python技术"},
-    {"nickname_list": ["奇妙的linux世界","k8s技术圈","阿里云云原生","云原生实验室","分布式实验室","漫谈云原生","云原生生态圈","崔亮的博客"],"tag": "容器技术"},
+    {"nickname_list": ["进击的Coder","Python爬虫与数据挖掘","Python绿色通道","Python开发者","Python实用宝典"],"tag": "Python技术"},^M
+    {"nickname_list": ["奇妙的linux世界","k8s技术圈","阿里云云原生","云原生实验室","分布式实验室","漫谈云原生","云原生生态圈","云原生社区动态","崔亮的博客"],"t
ag": "容器技术"},^M
     {"nickname_list": ["InfoQ","技术琐话","大魏分享","超级架构师","架构师技术联盟","中间件兴趣圈","InfoQ 架构头条","全栈云技术架构"],"tag": "架构技术"},
     {"nickname_list": ["Linux云计算网络","极客重生","架构师之路"],"tag": "架构技术"},
     {"nickname_list": ["网络技术平台","洛神云网络技术","Linux云计算网络","云深知网络"],"tag": "网络技术"},
@@ -12,6 +12,6 @@
     {"nickname_list": ["玉刚说"],"tag": "Anroid技术"},
     {"nickname_list": ["军哥手记","特大号","云头条","yes的练级攻略"],"tag": "技术管理"},
     {"nickname_list": ["高效运维"],"tag": "运维技术"},
-    {"nickname_list": ["使用ftrace研究linux内核"],"tag": "Linux内核"}
+    {"nickname_list": ["使用ftrace研究linux内核","Linux内核之旅","Linux阅马场"],"tag": "Linux内核"}^M
   ]
 }
\ No newline at end of file

## 问题4：

2022-11-10 19:10:02,626 - INFO - {'watchlist': [{'nickname_list': ['梅森早知道', '大周早盘', '盘前早参'], 'tag': '股票早盘'}, {'nickname_list': ['大周午评', '盘中实战', '诗诗午评', '先知突发', '灵儿午评', '梅森午评'], 'tag': '股票午盘'}, {'nickname_list': ['梅森投研', '老樊研究院', '老樊机构投研', '先知研报', '大周先生', '野生分析老pu', '雪球不冷', '白鸦投研', '逻辑驱动交易'], 'tag': '股票晚盘'}, {'nickname_list': ['灵儿投研日记', '龙头情报局', '听玛玛的话', '常胜之军', '咸蛋黄的存钱罐', '无产阶级的法典'], 'tag': '股票晚盘'}, {'nickname_list': ['星辰投研', '调研心得', '90号股票', '价值事务所'], 'tag': '股票投研'}]}
2022-11-10 19:10:58,995 - ERROR - get_wx_gzh_one_page_articles content_dict={'base_resp': {'err_msg': 'invalid args', 'ret': 200002}}
2022-11-10 19:10:58,995 - ERROR - get_wx_gzh_one_page_articles failed,token=1416562959,nickname=盘前早8点
2022-11-10 19:10:58,996 - INFO - 抓取 盘前早8点 的文章,is_successful:False next_begin:-1
2022-11-10 19:11:03,274 - INFO - 抓取 梅森早知道 的文章,is_successful:True next_begin:912


## 今日学习

https://hub.docker.com/_/node/

https://juejin.cn/post/6937866755830349832


https://q.shanyue.tech/deploy/simple-nginx.html


## supervisorctl stop not working

[root@pcentos server]# supervisorctl stop eagleServer
eagleServer: stopped
[root@pcentos server]# netstat -anlp |grep :5000 
tcp        0      0 0.0.0.0:5000            0.0.0.0:*               LISTEN      2717/python         
[root@pcentos server]# 
[root@pcentos server]# 
[root@pcentos server]# ps -elf | grep 2717 
0 S root      2717     1  0  80   0 - 254784 futex_ 08:15 ?       00:00:03 python -u /root/eagleeye/sdist/server/eagle_server.py
0 S root      3976  3372  0  80   0 - 28207 pipe_r 08:22 pts/0    00:00:00 grep --color=auto 2717
[root@pcentos server]# 
[root@pcentos server]# kill -9 2717 


https://stackoverflow.com/questions/9090683/supervisord-stopping-child-processes



autorestart=true
stopsignal=KILL
 
 
[supervisor笔记](https://www.cnblogs.com/kevingrace/p/7525200.html)

3) Web Server：提供与supervisorctl功能相当的WEB操作界面
一个可以通过Web界面来查看和控制进程的状态，默认监听在9091上。

