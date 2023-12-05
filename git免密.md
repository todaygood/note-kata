

## git 配置 https免密码登录


一. 区分https clone 和 ssh clone
不同的克隆方式导致校验方式不同，对应的免秘方式也不一样。
https通过记住账号密码免登，ssh通过校验生成的密钥免登。 通常都用ssh校验。

二.https免密配置方法
设置配置 .git/config

git config --global credential.helper store
1                                                                                                                                                                                                                        
[credential]
helper = store

输入一次账号密码后第二次就会记住账号密码。
————————————————
版权声明：本文为CSDN博主「MYG_G」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_40657321/article/details/120848976