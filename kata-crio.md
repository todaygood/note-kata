# Ref doc 

https://blog.gmem.cc/kata-containers-study-note



kata container与kubernetes集成的几种方式
https://www.jianshu.com/p/0dc3c1a0c86c





# 安装k8s

CRI-O方案

CRI-O是基于OCI的CRI实现。 

## 安装skopeo

skopeo是一个命令行工具，支持对容器镜像、仓库进行各种操作。skopeo支持OCI镜像以及Docker v2镜像格式。skopeo可以和支持API v2的镜像仓库进行交互。

和Docker不同，skopeo不需要运行守护程序即可完成以下操作：

1. 在多种存储机制之间拷贝镜像，例如从一个仓库拷贝到另外一个
2. 查看远程镜像的属性，包括它的层，而不需要预先拉取到本地
3. 从仓库中删除镜像
4. 支持向仓库提供身份凭证信息



## 安装配置CRI-O



[crio.image]registries = ['docker.io'] [crio.runtime]manage_network_ns_lifecycle = **true**# 不受信任的负载用Kata运行runtime_untrusted_workload = "/usr/bin/kata-runtime"# 默认认为负载是受信任的default_workload_trust = "trusted"log_level = "info"

注意，CRI-O默认使用的运行时是runc。



### 启动crio 



systemctl start crio 



可以创建一个标注为“不受信任”的Pod，这样CRI-O将会以Kata作为运行时来运行它





# CRI-O支持一个节点上同时运行两种不同类型runtime的pod。

【katacontainer基础知识】kata-container介绍与原理

https://blog.csdn.net/zhonglinzhang/article/details/86489695



# 更换kata的hypervisor

参见：https://github.com/kata-containers/documentation/blob/master/how-to/run-kata-with-k8s.md

You can also add multiple entries to specify alternatives hypervisors, e.g.:

```
[crio.runtime.runtimes.kata-qemu]
  runtime_path = "/usr/bin/kata-runtime"
  runtime_type = "oci"

[crio.runtime.runtimes.kata-fc]
  runtime_path = "/usr/bin/kata-runtime"
  runtime_type = "oci"
```



# containerd vs crio

K8s根本甩不掉Docker，原因一说就懂
https://www.sohu.com/a/437295878_609552


容器技术之容器引擎与江湖门派

https://www.sohu.com/a/438547521_612370

