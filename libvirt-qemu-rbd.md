

理解 QEMU/KVM 和 Ceph（2）：QEMU 的 RBD 块驱动（block driver）
本系列文章会总结 QEMU/KVM 和 Ceph 之间的整合：

（1）QEMU-KVM 和 Ceph RBD 的 缓存机制总结

（2）QEMU 的 RBD 块驱动（block driver）

（3）存储卷挂接和设备名称

https://hk.soft.sh/rbd/libvirt/


https://www.cnblogs.com/sammyliu/p/5095976.html


https://documentation.suse.com/zh-cn/ses/6/html/ses-all/cha-ceph-kvm.html



### DNS TTL

https://jaminzhang.github.io/dns/DNS-TTL-Understanding-and-Config/



## eagle todo 

https://blog.csdn.net/Flag_ing/article/details/124061268

https://blog.csdn.net/sunjinjuan/article/details/79113120

https://www.cnblogs.com/muxiaomu/p/16650900.html

https://blog.csdn.net/t8116189520/article/details/91562915

[flask中设置响应信息的方法](https://blog.csdn.net/wei18791957243/article/details/85161993)

https://cloud.tencent.com/developer/article/1098894



http://flask_official.baoshu.red/testing.html



[http request 代码](https://www.twilio.com/blog/5-ways-http-requests-python)

[flaskr 教程](https://dormousehole.readthedocs.io/en/latest/tutorial/index.html)

https://tutorial.helloflask.com/hello/

[json](https://ahrilove.top/2020/03/10/json(%E4%BA%8C)/)




[分布式、多节点系统下定时任务重复执行问题解决方案](https://www.jianshu.com/p/756e44495cb4)
在分布式多节点系统下，或者是使用gunicorn等工具启用多个worker的情况下，如何保证后端的定时任务、初始化任务只执行一次呢？比如使用apscheduler或者flask-apscheduler实现的定时任务。

在这种情况下必须借助外部数据库才能实现，当然，不仅仅只能是Redis，你也可以利用当前系统下有的MySQL、或者MongoDB数据库，只需要自定义一张表，创建一个unique字段作为锁即可。

我这里将使用python语言，以MySQL为例，使用sqlalchemy+pymysql作为数据库操作方式，使用装饰器的方式对原有任务函数进行改造，以达到对分布式的支持。你可以将该方法扩散到其他语言、其他后端框架或者仅仅是定时任务后台的情况。

作者：越大大雨天
链接：https://www.jianshu.com/p/756e44495cb4
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


[python位置参数和关键字参数](https://zhuanlan.zhihu.com/p/412273465)

位置参数(positional): 传参时前面不带 "变量名=", 顺序不可变, 按顺序赋给相应的局部变量.关键字参数(keyword): 传参时前面加上 "变量名=", 顺序可变, 按名称赋给同名的局部变量.

[flask中的session](https://blog.csdn.net/qq_25672165/article/details/114581467)
是不是不用浏览器就没有意义？

Cookie和Session的区别联系
（1）区别：

cookies：保存在浏览器上，安全性低，cookie以文本格式存储，存储量有限；
session：保存在服务器上，安全性高，session一般存储在数据库，存储量较大；

（2）联系：

cookie的目的：

       由于HTTP是无状态的，在同一连接上连续执行的两个请求之间没有链接。对于试图与某些页面连贯地相互作用的用户而言，这立即存在问题。例如，我们在网页上登录某个网站时输入用户名及密码时如果保存为cookie，则每次我们访问的时候就不需要登录网站了，否则我们每次都必须输入用户信息，这是一个让人很不爽的事情。

session的目的：

       如果说Cookie机制是通过检查客户身上的“通行证”来确定客户身份的话，那么Session机制就是通过检查服务器上的“客户明细表”来确认客户身份。Session相当于程序在服务器上建立的一份客户档案，客户来访的时候只需要查询客户档案表就可以了。。

cookie和session是如何联系起来的：

       即服务器如何判断客户端发送过来的请求属于同一个会话？主要通过session id联系起来。

服务器、客户端如何获取sessionID?SessionID在期间是如何传输的？

       服务器第一次接收到请求时，会为这次请求开辟一块内存空间（创建了Session对象），同时生成一个Session id，并通过响应头的Set-Cookie：“JSESSIONID=XXXXXXX”命令，向客户端发送要求设置cookie的响应； 客户端收到响应后，在本机客户端设置了一个JSESSIONID=XXXXXXX的cookie信息，该cookie的过期时间为浏览器会话结束；

       接下来客户端每次向同一个网站发送请求时，请求头都会带上该cookie信息（包含Session id）； 然后，服务器通过读取请求头中的Cookie信息，获取名称为JSESSIONID的值，得到此次请求的Session id，以此来确认当前请求对象身份。
————————————————
版权声明：本文为CSDN博主「FatPuffer」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_42517220/article/details/88716135


## issue cannot pickle '_thread.lock' object

Exception in thread Thread-1:
Traceback (most recent call last):
  File "C:\Program Files\Python38\lib\threading.py", line 932, in _bootstrap_inner
    self.run()
  File "C:\Program Files\Python38\lib\threading.py", line 870, in run
    self._target(*self._args, **self._kwargs)
  File "D:\PycharmProjects\eagleeye\server\main.py", line 110, in add_cron_task_for_all_small_periods_for_all_users
    add_cron_task_for_all_small_periods('sanqiyanxishe@163.com')
  File "D:\PycharmProjects\eagleeye\server\main.py", line 130, in add_cron_task_for_all_small_periods
    scheduler.add_job(id="task_cron_for_each_small_period-{}".format(period),
  File "D:\PycharmProjects\eagleeye\venv\lib\site-packages\flask_apscheduler\scheduler.py", line 168, in add_job
    return self._scheduler.add_job(**job_def)
  File "D:\PycharmProjects\eagleeye\venv\lib\site-packages\apscheduler\schedulers\base.py", line 447, in add_job
    self._real_add_job(job, jobstore, replace_existing)
  File "D:\PycharmProjects\eagleeye\venv\lib\site-packages\apscheduler\schedulers\base.py", line 871, in _real_add_job
    store.add_job(job)
  File "D:\PycharmProjects\eagleeye\venv\lib\site-packages\apscheduler\jobstores\mongodb.py", line 89, in add_job
    'job_state': Binary(pickle.dumps(job.__getstate__(), self.pickle_protocol))
TypeError: cannot pickle '_thread.lock' object

在线程中 直接连数据库，之后就解决了，知识点：序列化。

## python 变量 

弱类型语言有两个特点：
变量无须声明就可以直接赋值，对一个不存在的变量赋值就相当于定义了一个新变量。
变量的数据类型可以随时改变，比如，同一个变量可以一会儿被赋值为整数，一会儿被赋值为字符串。

注意，弱类型并不等于没有类型！弱类型是说在书写代码时不用刻意关注类型，但是在编程语言的内部仍然是有类型的。我们可以使用 type() 内置函数类检测某个变量或者表达式的类型，例如：
>>> num = 10
>>> type(num)
<class 'int'>



## issue

2022-10-29 08:30:28,692 - INFO - Job "task_cron_for_each_small_period-1 (trigger: interval[0:01:00], next run at: 2022-10-29 08:31:28 CST)" executed successfully
2022-10-29 08:30:28,710 - ERROR - cron_task_for_each_small_period get_latest_quote failed,Working outside of request context.

This typically means that you attempted to use functionality that needed
an active HTTP request. Consult the documentation on testing for
information about how to avoid this problem.

参见： https://flask.palletsprojects.com/en/2.2.x/appcontext/#creating-an-application-context



## sqlite

https://www.runoob.com/sqlite/sqlite-commands.html


什么是ORM（Object Relation Mapping，对象关系映射）？

大多数编程语言平台是面向对象的。另一方面，RDBMS服务器中的数据存储为表。

对象关系映射是将对象参数映射到底层RDBMS表结构的技术。

ORM API提供了执行CRUD操作的方法，而不必编写原始SQL语句。


## 卡拉云低代码

https://my.kalacloud.com/apps



## issue  sqlite_db.py出现Working outside of application context.

  File "D:\PycharmProjects\eagleeye\venv\lib\site-packages\flask_sqlalchemy\extension.py", line 839, in _call_for_binds
    engine = self.engines[key]
  File "D:\PycharmProjects\eagleeye\venv\lib\site-packages\flask_sqlalchemy\extension.py", line 628, in engines
    app = current_app._get_current_object()  # type: ignore[attr-defined]
  File "D:\PycharmProjects\eagleeye\venv\lib\site-packages\werkzeug\local.py", line 513, in _get_current_object
    raise RuntimeError(unbound_message) from None
RuntimeError: Working outside of application context.

This typically means that you attempted to use functionality that needed
the current application. To solve this, set up an application context
with app.app_context(). See the documentation for more information.


在非 flask route函数中不要使用session变量。 

参见： https://stackoverflow.com/questions/68710021/flask-runtimeerror-working-outside-of-request-context

去掉session变量的使用之后，发现概率性的出现：

2022-10-29 17:39:30,421 - INFO - Running job "task_cron_for_each_small_period-1 (trigger: interval[0:01:00], next run at: 2022-10-29 17:40:30 CST)" (scheduled at 2022-10-29 17:39:30.393778+08:00)
2022-10-29 17:39:30,422 - INFO - Running job "task_cron_for_each_small_period-1 (trigger: interval[0:01:00], next run at: 2022-10-29 17:40:30 CST)" (scheduled at 2022-10-29 17:39:30.393778+08:00)
2022-10-29 17:39:30,427 - DEBUG - Next wakeup is due at 2022-10-29 17:40:30.393778+08:00 (in 59.980062 seconds)
2022-10-29 17:39:30,428 - DEBUG - Next wakeup is due at 2022-10-29 17:40:30.393778+08:00 (in 59.979062 seconds)
2022-10-29 17:39:30,462 - DEBUG - Resetting dropped connection: push2.eastmoney.com
2022-10-29 17:39:30,465 - DEBUG - Resetting dropped connection: push2.eastmoney.com
2022-10-29 17:39:30,669 - DEBUG - https://push2.eastmoney.com:443 "GET /api/qt/ulist.np/get?OSVersion=14.3&appVersion=6.3.8&fields=f12%2Cf14%2Cf3%2Cf2%2Cf15%2Cf16%2Cf17%2Cf4%2Cf8%2Cf10%2Cf9%2Cf5%2Cf6%2Cf18%2Cf20%2Cf21%2Cf13%2Cf124%2Cf297&fltt=2&plat=Iphone&product=EFund&secids=0.002415&serverVersion=6.3.6&version=6.3.8 HTTP/1.1" 200 349
2022-10-29 17:39:30,687 - DEBUG - https://push2.eastmoney.com:443 "GET /api/qt/ulist.np/get?OSVersion=14.3&appVersion=6.3.8&fields=f12%2Cf14%2Cf3%2Cf2%2Cf15%2Cf16%2Cf17%2Cf4%2Cf8%2Cf10%2Cf9%2Cf5%2Cf6%2Cf18%2Cf20%2Cf21%2Cf13%2Cf124%2Cf297&fltt=2&plat=Iphone&product=EFund&secids=0.002415&serverVersion=6.3.6&version=6.3.8 HTTP/1.1" 200 349
2022-10-29 17:39:30,721 - ERROR - cron_task_for_each_small_period get_latest_quote failed,Working outside of request context.

This typically means that you attempted to use functionality that needed
an active HTTP request. Consult the documentation on testing for
information about how to avoid this problem.
2022-10-29 17:39:30,721 - INFO - Job "task_cron_for_each_small_period-1 (trigger: interval[0:01:00], next run at: 2022-10-29 17:40:30 CST)" executed successfully
2022-10-29 17:39:32,807 - INFO - share_now_price_info_dict=代码                    002415
名称                      海康威视
涨跌幅                    -4.92
最新价                     28.4
最高                     29.77
最低                     28.11
今开                     29.75
涨跌额                    -1.47
换手率                     0.87
量比                      2.46
动态市盈率                  22.73
成交量                   799359
成交额            2285355056.67
昨日收盘                   29.87
总市值             267903127620
流通市值            260589088151
市场类型                      深A
行情ID                0.002415
更新时间     2022-10-28 15:34:45
最新交易日             2022-10-28
Name: 0, dtype: object monitor_info_dict={'_id': ObjectId('635be60757509c59cfa7bfe3'), 'username': 'sanqiyanxishe@163.com', 'code': '002415', 'stop_profit_price': 35.3, 'stop_loss_price': 28.5, 'target_purchase_price': 24.1, 'small_monitor_period': 1, 'upper_limit_in_small_period': 29.5, 'lower_limit_in_small_period': 28, 'big_monitor_period': 1, 'upper_limit_in_big_period': 35, 'lower_limit_in_big_period': 25}
2022-10-29 17:39:32,807 - INFO - Job "task_cron_for_each_small_period-1 (trigger: interval[0:01:00], next run at: 2022-10-29 17:40:30 CST)" executed successfully
2022-10-29 17:40:30,410 - DEBUG - Looking for jobs to run
2022-10-29 17:40:30,416 - INFO - Running job "task_cron_for_each_small_period-1 (trigger: interval[0:01:00], next run at: 2022-10-29 17:41:30 CST)" (scheduled at 2022-10-29 17:40:30.393778+08:00)
2022-10-29 17:40:30,417 - INFO - Running job "task_cron_for_each_small_period-1 (trigger: interval[0:01:00], next run at: 2022-10-29 17:41:30 CST)" (scheduled at 2022-10-29 17:40:30.393778+08:00)
2022-10-29 17:40:30,423 - DEBUG - Next wakeup is due at 2022-10-29 17:41:30.337265+08:00 (in 59.926287 seconds)
2022-10-29 17:40:30,423 - DEBUG - Next wakeup is due at 2022-10-29 17:41:30.337265+08:00 (in 59.926287 seconds)
2022-10-29 17:40:30,447 - DEBUG - Resetting dropped connection: push2.eastmoney.com
2022-10-29 17:40:30,469 - DEBUG - Resetting dropped connection: push2.eastmoney.com
2022-10-29 17:40:30,620 - DEBUG - https://push2.eastmoney.com:443 "GET /api/qt/ulist.np/get?OSVersion=14.3&appVersion=6.3.8&fields=f12%2Cf14%2Cf3%2Cf2%2Cf15%2Cf16%2Cf17%2Cf4%2Cf8%2Cf10%2Cf9%2Cf5%2Cf6%2Cf18%2Cf20%2Cf21%2Cf13%2Cf124%2Cf297&fltt=2&plat=Iphone&product=EFund&secids=0.002415&serverVersion=6.3.6&version=6.3.8 HTTP/1.1" 200 349
2022-10-29 17:40:30,641 - DEBUG - https://push2.eastmoney.com:443 "GET /api/qt/ulist.np/get?OSVersion=14.3&appVersion=6.3.8&fields=f12%2Cf14%2Cf3%2Cf2%2Cf15%2Cf16%2Cf17%2Cf4%2Cf8%2Cf10%2Cf9%2Cf5%2Cf6%2Cf18%2Cf20%2Cf21%2Cf13%2Cf124%2Cf297&fltt=2&plat=Iphone&product=EFund&secids=0.002415&serverVersion=6.3.6&version=6.3.8 HTTP/1.1" 200 349
2022-10-29 17:40:30,660 - ERROR - cron_task_for_each_small_period get_latest_quote failed,Working outside of request context.

This typically means that you attempted to use functionality that needed
an active HTTP request. Consult the documentation on testing for
information about how to avoid this problem.
2022-10-29 17:40:30,661 - INFO - Job "task_cron_for_each_small_period-1 (trigger: interval[0:01:00], next run at: 2022-10-29 17:41:30 CST)" executed successfully
2022-10-29 17:40:31,985 - INFO - share_now_price_info_dict=代码                    002415
名称                      海康威视
涨跌幅                    -4.92
最新价                     28.4
最高                     29.77
最低                     28.11
今开                     29.75
涨跌额                    -1.47
换手率                     0.87
量比                      2.46
动态市盈率                  22.73
成交量                   799359
成交额            2285355056.67
昨日收盘                   29.87
总市值             267903127620
流通市值            260589088151
市场类型                      深A
行情ID                0.002415
更新时间     2022-10-28 15:34:45
最新交易日             2022-10-28
Name: 0, dtype: object monitor_info_dict={'_id': ObjectId('635be60757509c59cfa7bfe3'), 'username': 'sanqiyanxishe@163.com', 'code': '002415', 'stop_profit_price': 35.3, 'stop_loss_price': 28.5, 'target_purchase_price': 24.1, 'small_monitor_period': 1, 'upper_limit_in_small_period': 29.5, 'lower_limit_in_small_period': 28, 'big_monitor_period': 1, 'upper_limit_in_big_period': 35, 'lower_limit_in_big_period': 25}
2022-10-29 17:40:31,985 - INFO - Job "task_cron_for_each_small_period-1 (trigger: interval[0:01:00], next run at: 2022-10-29 17:41:30 CST)" executed successfully

加了with 之后解决了。

