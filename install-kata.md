

# Ref 







2. 安装containerd , 

配置containerd 


3. 测试kata

参见

https://github.com/kata-containers/documentation/blob/master/how-to/containerd-kata.md#install-kata-containers



 CRI-O
 CRI-containerd  也叫containerd 

是两个CRI实现， 

Kata Containers 1.5 introduced the shimv2 for containerd 1.2.0, reducing the components required to spawn pods and containers, and this is the preferred way to run Kata Containers with Kubernetes (as documented here).

An equivalent shim implementation for CRI-O is planned.


containerd with CRI plugin
If you select containerd with cri plugin, follow the "Getting Started for Developers" instructions here to properly install it.

To customize containerd to select Kata Containers runtime, follow our "Configure containerd to use Kata Containers" internal documentation here.

所以我选择 CRI-containerd;但如果选择这种，我不能使用docker, https://github.com/kata-containers/documentation/blob/master/how-to/containerd-kata.md
使用的是ctr/crictl 等工具





# 安装k8s 

目前的主要问题是，Kata 不支持 host 网络。而 Kubernetes 中，etcd、nodelocaldns、kube-apiserver、kube-scheduler、metrics-server、node-exporter、kube-proxy、calico、kube-controller-manager 等，也就是 Static Pod 和 Daemonset 都会使用 host 网络。所以在安装部署时，依然使用 runc 作为默认的运行时，而将 kata-runtime 作为可选的运行时给特定的负载使用。

https://github.com/kata-containers/documentation/blob/master/how-to/run-kata-with-k8s.md


For Kata Containers 1.5.0-rc2 and above, we will use containerd-shim-kata-v2 (short as shimv2 in this documentation) to launch Kata Containers. For the previous version of Kata Containers, the Pods are launched with kata-runtime.


安装k8s 使用kata 
https://github.com/kata-containers/documentation/blob/master/how-to/how-to-use-k8s-with-cri-containerd-and-kata.md#configure-containerd-to-use-kata-containers

 

# Troubleshoot Kata Containers


https://github.com/kata-containers/documentation/blob/master/Developer-Guide.md#run-kata-containers-with-kubernetes


cri-containerd-1.2.4.linux-amd64.tar.gz





# crictl 

拉取镜像，默认配置的默认传输是docker://，因此可以直接使用docker pull语法拉取Docker镜像：

```bash 
crictl pull --creds USERNAME[:PASSWORD] NAME[:TAG|@DIGEST]

crictl inspecti  docker.gmem.cc/ubuntu:16.04
```


显示容器运行时的信息：

```
 crictl info 4ae949f913362 
{
  "status": {
    "conditions": [
      {
        "type": "RuntimeReady", # 运行时状态
      },
      {
        "type": "NetworkReady", # 网络状态
      }
    ]
  }
}
```

运行一个新的Pod： crictl runp pod-config.yaml

```
[root@node1 kubesphere-project]# crictl  imagefsinfo hub-mirror.c.163.com/library/busybox
{
  "status": {
    "timestamp": "1608470339222756886",
    "fsId": {
      "mountpoint": "/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs"
    },
    "usedBytes": {
      "value": "517160388"
    },
    "inodesUsed": {
      "value": "7033"
    }
  }
}
```

```
# 查看Pod

crictl pods

\# POD ID       CREATED       STATE        NAME               NAMESPACE      ATTEMPT

\# 6914a958097c2    25 minutes ago   Ready        speaker-qkng4          metallb-system   0

 

# 查看容器

crictl **ps**

# CONTAINER ID    IMAGE   CREATED       STATE        NAME 
```



# 问题总结





使用kubeadm安装时不启动docker , 直接 init 就使用containerd ， 出现问题，参见：issue-kubeadm-init-containerd.md 文件

如果kubeadm 安装时，仍然使用docker , 等k8s启动之后，再把kata加入到run time 中去，根据文档：如何在 Kubernetes 集群集成 Kata

https://www.chenshaowen.com/blog/how-to-integrate-kata-in-kubernetes-cluster.html

 这种方式在我现在这个版本上出现：

1. 已经安装好的kube-apiserver，calico 等系统pod , 都起不来 ,只起来了一个Pod



```
[root@node1 deploy-k8s1]# crictl ps -a
CONTAINER           IMAGE               CREATED             STATE               NAME                      ATTEMPT             POD ID
0726849770804       3971f13f2c6c9       10 minutes ago      Running             calico-kube-controllers   0                   6304a2c6118e9
ecb3673ec6041       6912ec2cfae66       17 minutes ago      Exited              install-cni               0                   e44265d3dc594
dda15bcfa366f       6912ec2cfae66       17 minutes ago      Exited              upgrade-ipam              0                   e44265d3dc594

[root@node1 deploy-k8s1]# ps aux |grep kubelet
root      1186  0.0  0.0 112824  2388 pts/4    S+   16:38   0:00 grep --color=auto kubelet
root     27173 58.5  2.7 1627852 110324 ?      Ssl  16:16  13:05 /usr/bin/kubelet --container-runtime=remote --runtime-request-timeout=15m --container-runtime-endpoint=unix:///run/containerd/containerd.sock --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --network-plugin=cni --pod-infra-container-image=k8s.gcr.io/pause:3.2


[root@node1 deploy-k8s1]# systemctl status containerd
● containerd.service - containerd container runtime
   Loaded: loaded (/etc/systemd/system/containerd.service; enabled; vendor preset: disabled)
   Active: active (running) since Mon 2020-12-21 16:26:52 CST; 10min ago
     Docs: https://containerd.io
  Process: 31141 ExecStartPre=/sbin/modprobe overlay (code=exited, status=0/SUCCESS)
 Main PID: 31143 (containerd)
    Tasks: 175
   Memory: 942.1M
   CGroup: /system.slice/containerd.service
           ├─27399 /usr/local/bin/containerd-shim-runc-v2 -namespace k8s.io -id 0c6617ad09596255c21915de750cfd36f76e2a74d05158f3813b58c502ab2c38 -address /run/containe...
           ├─27400 /usr/local/bin/containerd-shim-runc-v2 -namespace k8s.io -id 74fcae043ab6eca128381dc4b2c7f371fb5d5c2af17d97f24c72cecb28ac2a8c -address /run/containe...
           ├─27401 /usr/local/bin/containerd-shim-runc-v2 -namespace k8s.io -id 310f5acd5f58cc25c00eee1afbdf5d426c1ea6e2344a5278f891a059a649583b -address /run/containe...
           ├─27402 /usr/local/bin/containerd-shim-runc-v2 -namespace k8s.io -id 1d1da7ce12dc47b055f792dd373e6dea1341f352c302ff5134d717197bb89bab -address /run/containe...
           ├─27510 /usr/local/bin/containerd-shim-runc-v2 -namespace k8s.io -id b6631dc27e69332c6cb31f3f14660589d8b71b81b84a07fe73207daa29fb2a6e -address /run/containe...
           ├─27586 /usr/local/bin/containerd-shim-runc-v2 -namespace k8s.io -id e44265d3dc59467ff37bc8ab6ae67a962f8274d183deb233bd4dbcf27613e525 -address /run/containe...
           ├─27597 /usr/local/bin/containerd-shim-runc-v2 -namespace k8s.io -id d99aec844fa4e907df60b583c432b9c201484d99640f79ff0463960f9a59d300 -address /run/containe...
           ├─27614 /usr/local/bin/containerd-shim-runc-v2 -namespace k8s.io -id a6932e39bbcaf4f42b831d18c0f160329a17e250d08d71c342a66892232f7f5d -address /run/containe...
           ├─27784 /usr/local/bin/containerd-shim-runc-v2 -namespace k8s.io -id 6304a2c6118e9cddfbe91e9faea05e0a53f2e41e6ec8046f2f7810701bcee116 -address /run/containe...
           ├─27936 /usr/bin/containerd-shim-kata-v2 -namespace k8s.io -address /run/containerd/containerd.sock -publish-binary /usr/local/bin/containerd -id d62cdab302...
           ├─27951 /usr/bin/qemu-vanilla-system-x86_64 -name sandbox-d62cdab30280aeee95683207e64af0a0ee1febcaa87c949026cca942b3f34227 -uuid d7d73cf1-8154-4618-9873-534...
           ├─28131 /usr/local/bin/containerd-shim-runc-v2 -namespace k8s.io -id 9596b3d28149bda3aa11c7332026a1e901b0e61ea8c7682d6383f4b0801598a7 -address /run/containe...
           ├─31143 /usr/local/bin/containerd
           ├─31343 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/dd732c1a96a1db52d6f29ab49410f1ae76e1f660055c7cf270f...
           ├─kubepods-burstable-pod0aeabbe5_828c_45ec_9fc4_2e54b2157cf4.slice:cri-containerd:d99aec844fa4e907df60b583c432b9c201484d99640f79ff0463960f9a59d300
           │ └─27666 /pause
           ├─kubepods-burstable-podf543c94683059cb32a4441e29fbdb238.slice:cri-containerd:1d1da7ce12dc47b055f792dd373e6dea1341f352c302ff5134d717197bb89bab
           │ └─27517 /pause
           ├─kubepods-burstable-podec39ffac_7e61_492d_8106_70dc9e62e964.slice:cri-containerd:a6932e39bbcaf4f42b831d18c0f160329a17e250d08d71c342a66892232f7f5d
           │ └─27662 /pause
           ├─kubepods-burstable-pod2c131162_5828_4c4b_8c73_b37c661fe540.slice:cri-containerd:e44265d3dc59467ff37bc8ab6ae67a962f8274d183deb233bd4dbcf27613e525
           │ └─27665 /pause
           ├─kubepods-besteffort-pod1a238d8f461404b1fb6f1276905b36fb.slice:cri-containerd:74fcae043ab6eca128381dc4b2c7f371fb5d5c2af17d97f24c72cecb28ac2a8c
           │ └─27498 /pause
           ├─kubepods-besteffort-pod5ed1bf41_04e1_4269_b214_d2c953700e16.slice:cri-containerd:b6631dc27e69332c6cb31f3f14660589d8b71b81b84a07fe73207daa29fb2a6e
           │ └─27594 /pause
           ├─kubepods-besteffort-podfdb19926_b48b_4d9e_87f2_f6febe9be928.slice:cri-containerd:9596b3d28149bda3aa11c7332026a1e901b0e61ea8c7682d6383f4b0801598a7
           │ └─28158 /pause
           ├─kubepods-besteffort-pod15842298_6e58_46dd_820f_4f0f76b3a1a5.slice:cri-containerd:0726849770804867b295223ed09e37b39f46e312ebc88f252ba24cf070416ef5
           │ └─30511 /usr/bin/kube-controllers
           ├─kubepods-burstable-podc8deb031a9d0ee2cd04c5ac947ebf851.slice:cri-containerd:310f5acd5f58cc25c00eee1afbdf5d426c1ea6e2344a5278f891a059a649583b
           │ └─27554 /pause
           ├─kubepods-burstable-pod3c13e7a522d92017534be343efdf8d9c.slice:cri-containerd:0c6617ad09596255c21915de750cfd36f76e2a74d05158f3813b58c502ab2c38
           │ └─27551 /pause
           └─kubepods-besteffort-pod15842298_6e58_46dd_820f_4f0f76b3a1a5.slice:cri-containerd:6304a2c6118e9cddfbe91e9faea05e0a53f2e41e6ec8046f2f7810701bcee116
             └─27804 /pause

Dec 21 16:37:11 node1 kata[27936]: time="2020-12-21T16:37:11.36386091+08:00" level=info msg="{\"return\": [{\"arch\": \"x86\", \"current\": true, \"props\": {\"core-id...
Dec 21 16:37:11 node1 containerd[31143]: time="2020-12-21T16:37:11.363860910+08:00" level=info msg="{\"return\": [{\"arch\": \"x86\", \"current\": true, \"props\": {\"...
Dec 21 16:37:11 node1 kata[27936]: time="2020-12-21T16:37:11.426225743+08:00" level=info msg="Container is started" ID=d62cdab30280aeee95683207e64af0a0ee1fe...tem=sandbox
Dec 21 16:37:11 node1 containerd[31143]: time="2020-12-21T16:37:11.426225743+08:00" level=info msg="Container is started" ID=d62cdab30280aeee95683207e64af0a0...em=sandbox
Dec 21 16:37:11 node1 containerd[31143]: time="2020-12-21T16:37:11.438221207+08:00" level=info msg="StartContainer for \"49ff60cdca0e3297888aa73e8b1cde9eb60a...cessfully"
Dec 21 16:37:15 node1 containerd[31143]: time="2020-12-21T16:37:15.225229170+08:00" level=info msg="ExecSync for \"0726849770804867b295223ed09e37b39f46e312eb...out 1 (s)"
Dec 21 16:37:15 node1 containerd[31143]: time="2020-12-21T16:37:15.305046770+08:00" level=info msg="Finish piping \"stderr\" of container exec \"eb75a4698c34...ab01504\""
Dec 21 16:37:15 node1 containerd[31143]: time="2020-12-21T16:37:15.305149714+08:00" level=info msg="Finish piping \"stdout\" of container exec \"eb75a4698c34...ab01504\""
Dec 21 16:37:15 node1 containerd[31143]: time="2020-12-21T16:37:15.306670836+08:00" level=info msg="Exec process \"eb75a4698c34d29454b3403baf4ec74eafae75ba73...ror <nil>"
Dec 21 16:37:15 node1 containerd[31143]: time="2020-12-21T16:37:15.309408532+08:00" level=info msg="ExecSync for \"0726849770804867b295223ed09e37b39f46e312eb...it code 1"
Hint: Some lines were ellipsized, use -l to show in full.
[root@node1 deploy-k8s1]#

```





## docker 使用5.9.14-1.el7.elrepo.x86_64内核时告警

Dec 21 15:51:58 node1 dockerd[31379]: time="2020-12-21T15:51:58.341333906+08:00" level=info msg="[graphdriver] using prior storage driver: overlay2"
Dec 21 15:51:59 node1 dockerd[31379]: time="2020-12-21T15:51:59.100442400+08:00" level=warning msg="Your kernel does not support cgroup blkio weight"
Dec 21 15:51:59 node1 dockerd[31379]: time="2020-12-21T15:51:59.100538024+08:00" level=warning msg="Your kernel does not support cgroup blkio weight_device"