
## 今天尝试抓了几个股票的代码，深证的代码都是以00或000开头，虽然已经转化为str，但写入csv或excel仍出现0丢失的问题，尝试了多种办法，最后发现，在代码前加入’\t’是最优解决方案。
代码如下:

code = '000123'
with open(stock.csv,'w') as f:
    f.write('\t'+code)
f.clsoe()
————————————————
版权声明：本文为CSDN博主「White_Mountain」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/cwjcw81/article/details/113839714


使用pandas将csv文件转成xlsx文件  

https://blog.csdn.net/qq_33689414/article/details/78307031


## bug: get token 

https://mp.weixin.qq.com/s/t0S9TIxJt6ER7YU1lnCf0g

C:\Users\HuJun\Documents\down_wechat_articles\venv\Scripts\python.exe C:/Users/HuJun/Documents/down_wechat_articles/get_fakeid_from_url.py
https://mp.weixin.qq.com/s/t0S9TIxJt6ER7YU1lnCf0g,1
九边:MzUzMjY0NDY4Ng==\x26amp;amp;mid=2247493489\x26amp;amp;idx=1\x26amp;amp;sn=d383f8023e7aa28b0cb4e38025b41b10\x26amp;amp;chksm=fab2b150cdc53846f1f5c8829e995a581130296511cd72e4413a96e6b3236d419c8a0fc91f13\x26amp;amp;scene=142#wechat_redirect\x26quot;\x26gt;一文说清楚军统、中统、中央俱乐部的来龙去脉\x26lt;/a\x26gt;
https://mp.weixin.qq.com/s/jbPWs-UItd62hP2y72_oIg,1
天涯时事:MzI1OTM5OTY0Mw==
https://mp.weixin.qq.com/s/TTJs1Fqic5xzNMjeHjasbA,1


## bug: eagleServer 小周期 重复告警 



## sql 语句 

https://devopedia.org/mongodb-query-language







 
