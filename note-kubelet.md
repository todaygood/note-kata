# kubelet参数修改



下面是我最终梳理的，认为需要真正显示设置的flag，如下：

```
/usr/bin/kubelet` `—address=0.0.0.0 --port=10250 --allow-privileged=``false` `--cluster-dns=10.0.0.1 --cluster-domain=caas.vivo.com --max-pods=50 --network-plugin=cni --require-kubeconfig --pod-manifest-path=``/etc/kubelet``.d/ --pod-infra-container-image=vivo.registry.com``/google_containers/pause-amd64``:3.0 --eviction-hard=memory.available<4Gi,nodefs.available<20Gi,imagefs.available<5Gi --eviction-max-pod-grace-period=30 --eviction-minimum-reclaim=memory.available=500Mi,nodefs.available=2Gi,imagefs.available=2Gi --eviction-pressure-transition-period=5m0s --eviction-soft=memory.available<8Gi,nodefs.available<100Gi,imagefs.available<20Gi --eviction-soft-grace-period=memory.available=30s,nodefs.available=2m,imagefs.available=2m --experimental-kernel-memcg-notification --experimental-fail-swap-on --system-reserved=cpu=100m,memory=8G --kube-reserved=cpu=200m,memory=16G --hairpin-mode=promiscuous-bridge --image-gc-high-threshold=60 --image-gc-low-threshold=40 --serialize-image-pulls=``false` `--protect-kernel-defaults --feature-gates=AllAlpha=``false
```

```
kubelet --experimental-allowed-unsafe-sysctls 'kernel.msg*,kernel.shmmax,kernel.sem,net.ipv4.route.min_pmtu'
修改方法：修改环境变量文件/etc/kubernetes/kubelet.env，修改为
（添加了--experimental-allowed-unsafe-sysctls='kernel.msg*,kernel.shmmax,kernel.sem,net.ipv4.route.min_pmtu'"）
```

# [K8S实用整理(08)-kubelet启动参数修改方法(配置Enabling Unsafe Sysctls)](https://www.cnblogs.com/DaweiJ/articles/8529706.html)

# 配置kubelet的垃圾回收



https://kuboard.cn/learning/k8s-advanced/gc.html



- [镜像回收](https://kuboard.cn/learning/k8s-advanced/gc.html#镜像回收)
- [容器回收](https://kuboard.cn/learning/k8s-advanced/gc.html#容器回收)