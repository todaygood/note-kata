

#  PostgreSQL的登录、创建用户、数据库并赋权

https://blog.csdn.net/Rong_Toa/article/details/88917424

[root@pcentos postgres]# docker exec -it postgres_db_1 bash
root@fe28aa115d87:/# su - postgres
postgres@fe28aa115d87:~$ psql
psql (14.2 (Debian 14.2-1.pgdg110+1))
Type "help" for help.

postgres=# create user dfr with password 'todayGood123';
CREATE ROLE
postgres=# create database dfr owner dfr;
ERROR:  database "dfr" already exists
postgres=# create database dfr owner dfr;
CREATE DATABASE
postgres=# grant all privileges on database dfr to dfr;
GRANT
postgres=# \q
postgres@fe28aa115d87:~$ psql -d dfr
psql (14.2 (Debian 14.2-1.pgdg110+1))
Type "help" for help.

dfr=# show tables;
ERROR:  unrecognized configuration parameter "tables"
dfr=# 


Postgresql存放数组形式的数据
https://blog.csdn.net/weixin_30394633/article/details/99919416

