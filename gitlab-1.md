## 一起直接断电导致数据库起不来的问题

gitlab web访问不了， 发现是gitlab 的postgresql down 了 

https://blog.51cto.com/shaoniana/5468847


```bash
[root@pcentos var]# find . -name postmaster.pid
./opt/gitlab/postgresql/data/postmaster.pid

[root@pcentos data]# gitlab-ctl tail postgresql 
==> /var/log/gitlab/postgresql/state <==

==> /var/log/gitlab/postgresql/current <==
2022-12-04_09:17:41.07398 HINT:  Is another postmaster (PID 2707) using socket file "/var/opt/gitlab/postgresql/.s.PGSQL.5432"?
2022-12-04_09:17:41.10745 LOG:  database system is shut down
2022-12-04_09:17:42.35783 LOG:  starting PostgreSQL 13.6 on x86_64-pc-linux-gnu, compiled by gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-44), 64-bit
2022-12-04_09:17:42.35795 FATAL:  lock file "/var/opt/gitlab/postgresql/.s.PGSQL.5432.lock" already exists
2022-12-04_09:17:42.35796 HINT:  Is another postmaster (PID 2707) using socket file "/var/opt/gitlab/postgresql/.s.PGSQL.5432"?
2022-12-04_09:17:42.38284 LOG:  database system is shut down
2022-12-04_09:17:43.73857 LOG:  starting PostgreSQL 13.6 on x86_


[root@pcentos postgresql]# rm -f .s.PGSQL.5432.lock
[root@pcentos postgresql]# gitlab-ctl restart postgresql 
ok: run: postgresql: (pid 32746) 0s
[root@pcentos postgresql]# gitlab-ctl status 
run: alertmanager: (pid 2673) 9713s; run: log: (pid 2672) 9713s
run: gitaly: (pid 2685) 9713s; run: log: (pid 2668) 9713s
run: gitlab-exporter: (pid 2688) 9713s; run: log: (pid 2687) 9713s
run: gitlab-kas: (pid 2689) 9713s; run: log: (pid 2669) 9713s
run: gitlab-workhorse: (pid 2695) 9713s; run: log: (pid 2679) 9713s
run: grafana: (pid 2675) 9713s; run: log: (pid 2674) 9713s
run: logrotate: (pid 16674) 2513s; run: log: (pid 2676) 9713s
run: node-exporter: (pid 2694) 9713s; run: log: (pid 2678) 9713s
run: postgres-exporter: (pid 2671) 9713s; run: log: (pid 2670) 9713s
run: postgresql: (pid 32746) 16s; run: log: (pid 2666) 9713s
run: prometheus: (pid 2684) 9713s; run: log: (pid 2683) 9713s
run: puma: (pid 32444) 47s; run: log: (pid 2680) 9713s
run: redis: (pid 2697) 9713s; run: log: (pid 2681) 9713s
run: redis-exporter: (pid 2692) 9713s; run: log: (pid 2690) 9713s
run: sidekiq: (pid 32454) 44s; run: log: (pid 2667) 9713s

```

## gitlab 数据迁移

https://blog.51cto.com/20101218/2807572

三、   定时备份
计划每天凌晨五点进行备份

 

[root@localhost ~]# crontab -l

0 5 * * * /opt/gitlab/bin/gitlab-rake gitlab:backup:create CRON=1