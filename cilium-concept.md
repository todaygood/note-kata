

# Cilium 概念

This is the simplest installation method as it only depends on Kubernetes and does not require additional external dependencies. It is a good option for environments up to about 250 nodes. For bigger environments or for environments which want to leverage the clustermesh functionality, a kvstore set up is required which can be set up using an [Installation with external etcd](https://docs.cilium.io/en/v1.9/gettingstarted/k8s-install-external-etcd/#k8s-install-etcd) or using the [Installation with managed etcd](https://docs.cilium.io/en/v1.9/gettingstarted/k8s-install-etcd-operator/#k8s-install-etcd-operator).



小于250个nodes的环境， 不需要安装KV  database , 否则需要一套专门的etcd集群给cilium 使用。 

It is possible to leverage the etcd cluster of Kubernetes directly or to maintain a dedicated etcd cluster.

参见：https://docs.cilium.io/en/v1.9/concepts/overview/





## 架构

![image-20201230195325718](C:\Users\margi\AppData\Roaming\Typora\typora-user-images\image-20201230195325718.png)



各组件介绍， 参见： https://docs.cilium.io/en/v1.9/concepts/overview/





Setup Helm repository:

> ```
> helm repo add cilium https://helm.cilium.io/
> ```
>
> ![img](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABwAAAAcCAMAAABF0y+mAAAABGdBTUEAALGPC/xhBQAAAAFzUkdCAK7OHOkAAACiUExURUdwTAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAJkEv5MAAAA1dFJOUwAwnr/H5BShnG3DCRPPm5OdyC4jGzHhB7o4AmkDyTJE1vn6ymWjbAHtTIQE6dwNoGH71I2zlJ78SQAAAI9JREFUKM/t0kcOwkAMhWGnTZhUOoQWeu8w979aJEDiOWhGOQD/wpL1LbwxER32ivUU9E0e6zXsngDOfWLJGBZVwl36i6H4FL3mCDGfeqygDegkFjYZ9wEbQ37ZVoCu5Oj/sSLaBnSCTjPUYSv2upkOqSeigRbJcPPdYs1xNYMlPbHvvWyXgOcHe/vbdUPmCj5KFX8s6tJBAAAAAElFTkSuQmCC) Copy Line

Deploy Cilium release via Helm:

```
helm install cilium cilium/cilium --version 1.9.1 \
  --namespace=kube-system \
  --set cni.chainingMode=generic-veth \
  --set cni.customConf=true \
  --set cni.configMap=cni-configuration \
  --set tunnel=disabled \
  --set masquerade=false \
  --set enableIdentityMark=false
```