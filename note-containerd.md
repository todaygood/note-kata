

1. 一些功能不再支持，比如 与构建镜像相关的操作。所以，在将k8s作为流水线的执行环境时，构建镜像需要使用dind的方式。
2. containerd将不再能看到contianer的运行情况，需要使用crictl相关命令查看。但是crictl是从kubelet的视角上看到的，它操作的对象级别是pod，而不是单个容器。



# 国内docker hub 镜像

https://www.chenshaowen.com/blog/how-to-cross-the-limit-of-dockerhub.html



# 使用 crictl 对 Kubernetes 节点进行调试

https://kubernetes.io/zh/docs/tasks/debug-application-cluster/crictl/





# kubelet config



kubelet开始使用config文件来配置参数:

https://kubernetes.io/docs/tasks/administer-cluster/kubelet-config-file/

参见源代码：

https://github.com/kubernetes/kubernetes/blob/release-1.11/pkg/kubelet/apis/kubeletconfig/v1beta1/types.go







Dec 20 08:34:55 node1 kubelet[4938]: Flag --runtime-request-timeout has been deprecated, This parameter should be set via the config file specified by the Kubelet's --config flag. See https://kubernetes.io/docs/tasks/administer-cluster/kubelet-config-file/ for more information.



Dec 20 08:34:55 node1 kubelet[4938]: Flag --cgroup-driver has been deprecated, This parameter should be set via the config file specified by the Kubelet's --config flag. See https://kubernetes.io/docs/tasks/administer-cluster/kubelet-config-file/ for more information.

```
-cgroup-driver=cgroupfs
```



By default, the cgroup driver of cri is configured as `cgroupfs`. If this is not the case, you can switch to `systemd` manually in `edgecore.yaml`

ref: https://docs.kubeedge.io/en/latest/configuration/cri.html



我看多个文章，都修改了这个参数， 使用 systemd作为cgroup driver ，好像更香。 



# TODo



 git clone https://github.com/kubesphere/porter.git

git clone https://github.com/kubesphere/kubesphere.git



```
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
RuntimeRequestTimeout: 15m
evictionHard:
    memory.available:  "200Mi"
```







```
[root@node1 ~]# ctr plugin ls
TYPE                            ID                       PLATFORMS      STATUS
io.containerd.content.v1        content                  -              ok
io.containerd.snapshotter.v1    aufs                     linux/amd64    error
io.containerd.snapshotter.v1    btrfs                    linux/amd64    error
io.containerd.snapshotter.v1    devmapper                linux/amd64    error
io.containerd.snapshotter.v1    native                   linux/amd64    ok
io.containerd.snapshotter.v1    overlayfs                linux/amd64    ok
io.containerd.snapshotter.v1    zfs                      linux/amd64    error
io.containerd.metadata.v1       bolt                     -              ok
io.containerd.differ.v1         walking                  linux/amd64    ok
io.containerd.gc.v1             scheduler                -              ok
io.containerd.service.v1        introspection-service    -              ok
io.containerd.service.v1        containers-service       -              ok
io.containerd.service.v1        content-service          -              ok
io.containerd.service.v1        diff-service             -              ok
io.containerd.service.v1        images-service           -              ok
io.containerd.service.v1        leases-service           -              ok
io.containerd.service.v1        namespaces-service       -              ok
io.containerd.service.v1        snapshots-service        -              ok
io.containerd.runtime.v1        linux                    linux/amd64    ok
io.containerd.runtime.v2        task                     linux/amd64    ok
io.containerd.monitor.v1        cgroups                  linux/amd64    ok
io.containerd.service.v1        tasks-service            -              ok
io.containerd.internal.v1       restart                  -              ok
io.containerd.grpc.v1           containers               -              ok
io.containerd.grpc.v1           content                  -              ok
io.containerd.grpc.v1           diff                     -              ok
io.containerd.grpc.v1           events                   -              ok
io.containerd.grpc.v1           healthcheck              -              ok
io.containerd.grpc.v1           images                   -              ok
io.containerd.grpc.v1           leases                   -              ok
io.containerd.grpc.v1           namespaces               -              ok
io.containerd.internal.v1       opt                      -              ok
io.containerd.grpc.v1           snapshots                -              ok
io.containerd.grpc.v1           tasks                    -              ok
io.containerd.grpc.v1           version                  -              ok
io.containerd.grpc.v1           cri                      linux/amd64    ok

```

