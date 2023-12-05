# coreDNS 

https://github.com/coredns/coredns

https://coredns.io/manual/toc/#installation

## coreDNS: 云原生DNS 


   基于 CoreDNS 和 K8s 构建云原生场景下的企业级 DNS, https://kubesphere.io/zh/blogs/coredns-k8s-dns/ ； 将CoreDNS放在应用商店里面供用户部署，使用openELB暴露服务出去。 



## coreDNS-nodecache

The Kubernetes Node-local dns add-on proposes running a DNS caching server on all of a Kubernetes cluster's nodes. The suggested caching server is node-cache, a thin wrapper around CoreDNS, that handles the setup & teardown of the dummy network interface & associated IPTables rules.

Coredns-nodecache is an attempt to implement node-cache as a CoreDNS plugin, rather than a wrapper. The motivations for this are:

the implementation relies only on CoreDNS Plugin API, which should be backward-compatible from version to version. This should make using the latest version of CoreDNS easier (see kubernetes/dns #306)
the configuration of nodecache would be done in the CoreDNS configuration file, instead of being split between the Corefile and command-line parameters.
Additionally, coredns-nodecache can provide a high-availability setup for Node-local.

https://github.com/contentful-labs/coredns-nodecache


## 方案

传统的dns技术还是有用的，新的云原生DNS是跑在k8s上的，如果没有k8s, 那就用传统技术,[BIND搭建主从DNS]
（https://developer.aliyun.com/article/491274） ， 或者使用一个单机的docker CoreDNS 。 



