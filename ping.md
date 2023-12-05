 

## 加 -DO 参数

[root@pcentos ~]# ping -DO 192.168.160.1 
PING 192.168.160.1 (192.168.160.1) 56(84) bytes of data.
[1678428801.479943] 64 bytes from 192.168.160.1: icmp_seq=1 ttl=64 time=21.7 ms
[1678428802.482859] 64 bytes from 192.168.160.1: icmp_seq=2 ttl=64 time=23.4 ms
[1678428803.527031] 64 bytes from 192.168.160.1: icmp_seq=3 ttl=64 time=66.0 ms
[1678428804.529129] 64 bytes from 192.168.160.1: icmp_seq=4 ttl=64 time=67.6 ms
[1678428805.479768] 64 bytes from 192.168.160.1: icmp_seq=5 ttl=64 time=16.5 ms
[1678428806.483766] 64 bytes from 192.168.160.1: icmp_seq=6 ttl=64 time=21.1 ms
[1678428807.485946] 64 bytes from 192.168.160.1: icmp_seq=7 ttl=64 time=22.5 ms
[1678428808.507950] 64 bytes from 192.168.160.1: icmp_seq=8 ttl=64 time=42.8 ms
[1678428809.481864] 64 bytes from 192.168.160.1: icmp_seq=9 ttl=64 time=15.3 ms
[1678428810.480943] 64 bytes from 192.168.160.1: icmp_seq=10 ttl=64 time=12.9 ms
[1678428811.491343] 64 bytes from 192.168.160.1: icmp_seq=11 ttl=64 time=22.9 ms
[1678428812.489342] 64 bytes from 192.168.160.1: icmp_seq=12 ttl=64 time=19.8 ms
[1678428813.486786] 64 bytes from 192.168.160.1: icmp_seq=13 ttl=64 time=15.4 ms
^C
--- 192.168.160.1 ping statistics ---
13 packets transmitted, 13 received, 0% packet loss, time 12013ms
rtt min/avg/max/mdev = 12.905/28.339/67.692/17.862 ms


$ oc -n myProject exec -it myPod -- /bin/bash -c "ping -c100 -DO 192.168.9.47"
PING 192.168.9.47 (192.168.9.47) 56(84) bytes of data.
[1631655329.893592] 64 bytes from 192.168.9.47: icmp_seq=1 ttl=64 time=1.02 ms
[1631655330.894070] 64 bytes from 192.168.9.47: icmp_seq=2 ttl=64 time=0.580 ms
[1631655331.902870] 64 bytes from 192.168.9.47: icmp_seq=3 ttl=64 time=0.081 ms
[1631655333.950726] no answer yet for icmp_seq=4
[1631655334.797234] 64 bytes from 192.168.9.47: icmp_seq=4 ttl=64 time=1868 ms
[1631655334.797625] 64 bytes from 192.168.9.47: icmp_seq=5 ttl=64 time=845 ms
[1631655334.950918] 64 bytes from 192.168.9.47: icmp_seq=6 ttl=64 time=0.096 ms
[1631655335.998876] 64 bytes from 192.168.9.47: icmp_seq=7 ttl=64 time=0.087 ms
[1631655337.022830] 64 bytes from 192.168.9.47: icmp_seq=8 ttl=64 time=0.095 ms
[1631655338.046893] 64 bytes from 192.168.9.47: icmp_seq=9 ttl=64 time=0.090 ms
[1631655339.070554] 64 bytes from 192.168.9.47: icmp_seq=10 ttl=64 time=0.081 ms
[1631655340.094585] 64 bytes from 192.168.9.47: icmp_seq=11 ttl=64 time=0.094 ms
[1631655341.118871] 64 bytes from 192.168.9.47: icmp_seq=12 ttl=64 time=0.094 ms
[1631655342.142778] 64 bytes from 192.168.9.47: icmp_seq=13 ttl=64 time=0.083 ms
[1631655343.166624] 64 bytes from 192.168.9.47: icmp_seq=14 ttl=64 time=0.097 ms
[1631655344.190548] 64 bytes from 192.168.9.47: icmp_seq=15 ttl=64 time=0.080 ms
[1631655345.214573] 64 bytes from 192.168.9.47: icmp_seq=16 ttl=64 time=0.093 ms
[1631655346.238578] 64 bytes from 192.168.9.47: icmp_seq=17 ttl=64 time=0.101 ms
[1631655347.262955] 64 bytes from 192.168.9.47: icmp_seq=18 ttl=64 time=0.096 ms
[1631655349.310494] no answer yet for icmp_seq=19
[1631655350.194241] 64 bytes from 192.168.9.47: icmp_seq=19 ttl=64 time=1905 ms
[1631655350.194579] 64 bytes from 192.168.9.47: icmp_seq=20 ttl=64 time=882 ms
[1631655350.309843] 64 bytes from 192.168.9.47: icmp_seq=21 ttl=64 time=0.089 ms
[1631655351.358847] 64 bytes from 192.168.9.47: icmp_seq=22 ttl=64 time=0.092 ms
[1631655352.382676] 64 bytes from 192.168.9.47: icmp_seq=23 ttl=64 time=0.105 ms
[1631655353.406813] 64 bytes from 192.168.9.47: icmp_seq=24 ttl=64 time=0.102 ms
[1631655354.430867] 64 bytes from 192.168.9.47: icmp_seq=25 ttl=64 time=0.084 ms
[1631655355.454559] 64 bytes from 192.168.9.47: icmp_seq=26 ttl=64 time=0.080 ms

