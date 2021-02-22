

# Ref 

如何在 Kubernetes 集群集成 Kata
https://www.chenshaowen.com/blog/how-to-integrate-kata-in-kubernetes-cluster.html

使用 Kubeadm 安装 Kubernetes 集群

https://www.chenshaowen.com/blog/using-kubeadm-to-install-the-kubernetes-cluster.html



k8s+containerd+kata-containers 安装。
https://ignorantshr.github.io/person-blog/kata-containers/k8s+containerd+kata-containers%20%E5%AE%89%E8%A3%85/



## 官方文档

https://github.com/kata-containers/documentation/blob/master/how-to/run-kata-with-k8s.md

https://github.com/kata-containers/documentation/blob/master/how-to/how-to-use-k8s-with-cri-containerd-and-kata.md

# 安装k8s 

目前的主要问题是，Kata 不支持 host 网络。而 Kubernetes 中，etcd、nodelocaldns、kube-apiserver、kube-scheduler、metrics-server、node-exporter、kube-proxy、calico、kube-controller-manager 等，也就是 Static Pod 和 Daemonset 都会使用 host 网络。所以在安装部署时，依然使用 runc 作为默认的运行时，而将 kata-runtime 作为可选的运行时给特定的负载使用。



## 安装kata

### 安装kata rpm包

For Kata Containers 1.5.0-rc2 and above, we will use `containerd-shim-kata-v2` (short as `shimv2` in this documentation) to launch Kata Containers. For the previous version of Kata Containers, the Pods are launched with `kata-runtime`.  我们用的是kata 1.7.3 版本；

执行命令 `kata-runtime kata-check` 检查系统是否支持运行 kata runtime，测试kata

参见

https://github.com/kata-containers/documentation/blob/master/how-to/containerd-kata.md#install-kata-containers

```bash
yum -y install kata-runtime kata-proxy kata-shim
```

#### 测试kata

```
[root@node1 ~]# kata-runtime -v
kata-runtime  : 1.7.3
   commit   : 2f88e2031f8756d0011c5328bcbdda051c7fb574
   OCI specs: 1.0.1-dev
```

执行命令 `kata-runtime kata-check` 检查系统是否支持运行 kata runtime



### 安装CRI containerd 

下载 cri-containerd-cni-1.4.3-linux-amd64.tar.gz， 遵照 Install Containerd with Release Tarball

https://github.com/containerd/cri/blob/master/docs/installation.md

具体在 /srv/http/kata-release/containerd/1.4.3 目录下运行3个脚本；

https://github.com/kata-containers/documentation/blob/master/how-to/run-kata-with-k8s.md

For Kata Containers 1.5.0-rc2 and above, we will use containerd-shim-kata-v2 (short as shimv2 in this documentation) to launch Kata Containers. For the previous version of Kata Containers, the Pods are launched with kata-runtime.



#### 配置containerd

containerd使用kata 时，配置参见: https://github.com/kata-containers/documentation/blob/master/how-to/containerd-kata.md

将runc 作为default runtime , kata作为second runtime

```
Kata Containers as a RuntimeClass
For

Kata Containers v1.5.0 or above (including 1.5.0-rc)
Containerd v1.2.0 or above
Kubernetes v1.12.0 or above
The RuntimeClass is suggested.

The following configuration includes three runtime classes:

plugins.cri.containerd.runtimes.runc: the runc, and it is the default runtime.
plugins.cri.containerd.runtimes.kata: The function in containerd (reference the document here) where the dot-connected string io.containerd.kata.v2 is translated to containerd-shim-kata-v2 (i.e. the binary name of the Kata implementation of Containerd Runtime V2 (Shim API)).
plugins.cri.containerd.runtimes.katacli: the containerd-shim-runc-v1 calls kata-runtime, which is the legacy process.
```







```csharp
[root@localhost bin]# cat /etc/containerd/config.toml 
root = "/var/lib/containerd"
state = "/run/containerd"
oom_score = 0

[grpc]
  address = "/run/containerd/containerd.sock"
  uid = 0
  gid = 0
  max_recv_message_size = 16777216
  max_send_message_size = 16777216

[debug]
  address = ""
  uid = 0
  gid = 0
  level = ""

[metrics]
  address = ""
  grpc_histogram = false

[cgroup]
  path = ""

[plugins]
  [plugins.cgroups]
    no_prometheus = false
  [plugins.cri]
    stream_server_address = ""
    stream_server_port = "10010"
    enable_selinux = false
    sandbox_image = "k8s.gcr.io/pause-amd64:3.1"
    stats_collect_period = 10
    systemd_cgroup = false
    enable_tls_streaming = false
    [plugins.cri.containerd]
      snapshotter = "overlayfs"
      ## default_runtime为默认runtime
      [plugins.cri.containerd.default_runtime]
        runtime_type = "io.containerd.runtime.v1.linux"
        runtime_engine = ""
        runtime_root = ""
      ## untrusted_workload_runtime当pod参数带有
      ## io.kubernetes.cri.untrusted-workload: "true"时生效
      [plugins.cri.containerd.untrusted_workload_runtime]
        runtime_type = "io.containerd.runtime.v1.linux"
        runtime_engine = "/usr/bin/kata-runtime"
        runtime_root = ""
    [plugins.cri.cni]
      bin_dir = "/opt/cni/bin"
      conf_dir = "/etc/cni/net.d"
      conf_template = ""
    [plugins.cri.registry]
      [plugins.cri.registry.mirrors]
        [plugins.cri.registry.mirrors."docker.io"]
          endpoint = ["https://9c56nwew.mirror.aliyuncs.com", "https://registry-1.docker.io"]
  [plugins.diff-service]
    default = ["walking"]
  [plugins.linux]
    shim = "containerd-shim"
    runtime = "runc"
    runtime_root = ""
    no_shim = false
    shim_debug = false
  [plugins.scheduler]
    pause_threshold = 0.02
    deletion_threshold = 0
    mutation_threshold = 100
    schedule_delay = "0s"
    startup_delay = "100ms"
```

各参数配置含义：https://github.com/containerd/cri/blob/release/1.0/docs/config.md



#### 测试containerd



参见 https://github.com/kata-containers/documentation/blob/master/how-to/containerd-kata.md

```
ctr image pull  hub-mirror.c.163.com/library/busybox:latest

ctr run  -t --rm hub-mirror.c.163.com/library/busybox:latest hello sh
```

Launch Pods with `crictl` command line

With the `crictl` command line of `cri-tools`, you can specify runtime class with `-r` or `--runtime` flag. Use the following to launch Pod with `kata` runtime class with the pod in [the example](https://github.com/kubernetes-sigs/cri-tools/tree/master/docs/examples) of `cri-tools`:

```
$ sudo crictl runp -r kata podsandbox-config.yaml
36e23521e8f89fabd9044924c9aeb34890c60e85e1748e8daca7e2e673f8653e
```

You can add container to the launched Pod with the following:

```
$ sudo crictl create 36e23521e8f89 container-config.yaml podsandbox-config.yaml
1aab7585530e62c446734f12f6899f095ce53422dafcf5a80055ba11b95f2da7
```

Now, start it with the following:

```bash
$ sudo crictl start 1aab7585530e6
1aab7585530e6
```

ctr 对标docker , crictl 是用来启动pod的， ctr 运行的容器,用crictl是查不出来的。 



 ctr run --runtime io.containerd.run.kata.v2 -t --rm docker.io/library/busybox:latest hello sh



# 问题集锦

## issue01 使用containerd之后拉取image失败


```
[root@node1 deploy-k8s1]#  kubeadm init --cri-socket /run/containerd/containerd.sock  --config=kubeadm-config.yaml --upload-certs
W1203 10:06:33.247320   24844 configset.go:348] WARNING: kubeadm cannot validate component configs for API groups [kubelet.config.k8s.io kubeproxy.config.k8s.io]
[init] Using Kubernetes version: v1.19.2
[preflight] Running pre-flight checks
        [WARNING Firewalld]: firewalld is active, please ensure ports [6443 10250] are open or your cluster may not function correctly
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
^C


```

参见containerd/docs/registry.md 文件中，先搭建一个https docker registry , 再配置/etc/containerd/config.toml, 

```
   # "plugins.cri.registry" contains config related to the registry
  [plugins.cri.registry]

    # "plugins.cri.registry.mirrors" are namespace to mirror mapping for all namespaces.
    [plugins.cri.registry.mirrors]
      [plugins.cri.registry.mirrors."docker.io"]
        endpoint = ["https://registry-1.docker.io", ]
    [plugins.cri.registry.configs."registry.pacloud.com".tls]
        ca_file   = "/etc/docker/certs.d/registry.pacloud.com/ca.crt"

```



测试

```
[root@node1 deploy-k8s1]# crictl pull registry.pacloud.com/k8s.gcr.io/kube-scheduler:v1.19.2
Image is up to date for sha256:2f32d66b884f8b7466f711970800143c815d0243b2ef1e84306108976845e24a

```





重启containerd服务。 	

```
[root@node1 ~]# crictl  pull registry.pacloud.com/k8s.gcr.io/pause:3.2
DEBU[0000] get image connection
DEBU[0000] connect using endpoint 'unix:///var/run/containerd/containerd.sock' with '10s' timeout
DEBU[0000] connected successfully using endpoint: unix:///var/run/containerd/containerd.sock
DEBU[0000] PullImageRequest: &PullImageRequest{Image:&ImageSpec{Image:registry.pacloud.com/k8s.gcr.io/pause:3.2,},Auth:n                                                     il,SandboxConfig:nil,}
DEBU[0000] PullImageResponse: &PullImageResponse{ImageRef:sha256:80d28bedfe5dec59da9ebf8e6260224ac9008ab5c11dbbe16ee3ba3                                                     e4439ac2c,}
Image is up to date for sha256:80d28bedfe5dec59da9ebf8e6260224ac9008ab5c11dbbe16ee3ba3e4439ac2c
```





## issue02 kubeadm init 失败





```bash
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[kubelet-check] Initial timeout of 40s passed.

	Unfortunately, an error has occurred:
		timed out waiting for the condition
	
	This error is likely caused by:
		- The kubelet is not running
		- The kubelet is unhealthy due to a misconfiguration of the node in some way (required cgroups disabled)
	
	If you are on a systemd-powered system, you can try to troubleshoot the error with the following commands:
		- 'systemctl status kubelet'
		- 'journalctl -xeu kubelet'
	
	Additionally, a control plane component may have crashed or exited when started by the container runtime.
	To troubleshoot, list all containers using your preferred container runtimes CLI.
	
	Here is one example how you may list all Kubernetes containers running in cri-o/containerd using crictl:
		- 'crictl --runtime-endpoint /run/containerd/containerd.sock ps -a | grep kube | grep -v pause'
		Once you have found the failing container, you can inspect its logs with:
		- 'crictl --runtime-endpoint /run/containerd/containerd.sock logs CONTAINERID'

error execution phase wait-control-plane: couldn't initialize a Kubernetes cluster
To see the stack trace of this error execute with --v=5 or higher
```

[root@node1 deploy-k8s1]# grep criSocket kubeadm-config-containerd.yaml 

criSocket: /run/containerd/containerd.sock

修改为：

 criSocket: /var/run/dockershim.sock

之后就没有问题了，k8s集群那些pod 还是使用docker来跑， kata用来跑业务pod 







## issue 03  必须要拉取k8s.gcr.io/pause:3.2





[root@node1 examples]# crictl runp -r kata podsandbox-config.yaml
FATA[0030] run pod sandbox: rpc error: code = Unknown desc = failed to get sandbox image "k8s.gcr.io/pause:3.2": failed to pull image "k8s.gcr.io/pause:3.2": failed to pull and unpack image "k8s.gcr.io/pause:3.2": failed to resolve reference "k8s.gcr.io/pause:3.2": failed to do request: Head "https://k8s.gcr.io/v2/pause/manifests/3.2": dial tcp 108.177.125.82:443: i/o timeout



crictl image 命令和 ctr image list 命令的输出，并不一样, 是因为用了namespace隔离

*If you need these images to be available to [Kubernetes](https://kubernetes.io/)*, you **must** be sure to add the `-n=k8s.io` flag to the `ctr image import` command, like this:

```
 ctr -n=k8s.io images import <filename-from-previous-step>
```

If you specified the `k8s.io` namespace when importing the images in the previous step—so as to make the images available to Kubernetes—then you can verify that CRI (Container Runtime Interface, the means by which Kubernetes talks to `containerd`) sees these images by running `crictl images` (again, `sudo` may be required, based on your configuration).

参见： 

https://blog.scottlowe.org/2020/01/25/manually-loading-container-images-with-containerd/



```
[root@node1 examples]# crictl run container-config.yaml podsandbox-config.yaml
FATA[0060] running container: run pod sandbox: rpc error: code = Unknown desc = failed to setup network for sandbox "52bac768e686e714e8c302bd20e46dc3dddc0f7738189f5650b75b79f35f2959": error getting ClusterInformation: Get https://[10.96.0.1]:443/apis/crd.projectcalico.org/v1/clusterinformations/default: dial tcp 10.96.0.1:443: i/o timeout
[root@node1 examples]#
[root@node1 examples]#
[root@node1 examples]# crictl runp -r kata podsandbox-config.yaml
FATA[0060] run pod sandbox: rpc error: code = Unknown desc = failed to setup network for sandbox "1f3bf9224ea54bd4bde7935b6f1692deceeef9628a56ccdf0598e9d168321793": error getting ClusterInformation: Get https://[10.96.0.1]:443/apis/crd.projectcalico.org/v1/clusterinformations/default: dial tcp 10.96.0.1:443: i/o timeout
[root@node1 examples]#

```

## issue04 kata虚拟机内部有一个agent ,问题是如何进入到这个虚拟机去查看？ 





## 成功安装kata 



[root@node1 deploy-k8s1]# kata-runtime list
ID                                                                 PID         STATUS      BUNDLE                                                                                                                  CREATED                          OWNER
07966941d491e16c867d85c93a8856a0292dc4fd72ef5f1cefd50277e3becb0e   -1          running     /run/containerd/io.containerd.runtime.v2.task/k8s.io/07966941d491e16c867d85c93a8856a0292dc4fd72ef5f1cefd50277e3becb0e   2020-12-21T10:59:40.320519284Z   #0
2771f2ccf03355c827aa19fd039b4c03784ec6394a92f2488ddf304de4a0b49e   -1          running     /run/containerd/io.containerd.runtime.v2.task/k8s.io/2771f2ccf03355c827aa19fd039b4c03784ec6394a92f2488ddf304de4a0b49e   2020-12-21T10:59:40.984492086Z   #0





### 如何查询容器?



根据container-id和 sandbox id，方法：

kubectl describe pod  得到container-id 

crictl inspect  <container-id>   |grep sandboxID   得到sandbox id 

[root@node1 deploy-k8s1]# crictl ps
CONTAINER           IMAGE               CREATED             STATE               NAME                      ATTEMPT             POD ID
2771f2ccf0335       ae2feff98a0cc       5 minutes ago       Running             nginx                     0                   07966941d491e
d593a291f9569       1558f4c6a111e       6 minutes ago       Running             app                       0                   af17ddaf44495
ae992272d9a38       bfe3a36ebd252       6 minutes ago       Running             coredns                   0                   5055b5e071758
bda6b80a3b507       bfe3a36ebd252       6 minutes ago       Running             coredns                   0                   f6b77d2bf3e5b
378ed4034d88d       3971f13f2c6c9       6 minutes ago       Running             calico-kube-controllers   0                   bf7ed29df9d9b
b1b93eb1cffd1       2e5029b93d4a9       6 minutes ago       Running             calico-node               0                   905c88d1ca22b
8e3d2ead5c8df       8603821e1a7a5       23 minutes ago      Running             kube-controller-manager   1                   31a11bbac7676
6d11dfb3b60fe       2f32d66b884f8       23 minutes ago      Running             kube-scheduler            1                   10b45b85aaa60
dd8bb6467b9ea       d1985d4043858       23 minutes ago      Running             etcd                      1                   cda2b7f1e89f2
813167bac1e9c       607331163122e       24 minutes ago      Running             kube-apiserver            1                   5dd78c50db759
206cfbdd235ba       d373dd5a8593a       25 minutes ago      Running             kube-proxy                0                   3e0b5979fddaf
[root@node1 deploy-k8s1]# crictl inspect 2771f2ccf0335

然后 ps axu 可以查询到相关的kata进程



![image-20201221190658151](C:\Users\margi\AppData\Roaming\Typora\typora-user-images\image-20201221190658151.png)





[root@node1 deploy-k8s1]# kubectl get pod -A
NAMESPACE     NAME                                       READY   STATUS    RESTARTS   AGE
default       busybox-f54ff6946-2t86r                    1/1     Running   0          19m
default       kata-nginx                                 1/1     Running   0          11m
kube-system   calico-kube-controllers-6c89d944d5-d262x   1/1     Running   0          12m
kube-system   calico-node-gtnrq                          1/1     Running   0          12m
kube-system   coredns-57658b797f-lpf8l                   1/1     Running   0          30m
kube-system   coredns-57658b797f-tz7qp                   1/1     Running   0          30m
kube-system   etcd-node1                                 1/1     Running   1          30m
kube-system   kube-apiserver-node1                       1/1     Running   1          30m
kube-system   kube-controller-manager-node1              1/1     Running   1          30m
kube-system   kube-proxy-c4r2j                           1/1     Running   0          30m
kube-system   kube-scheduler-node1                       1/1     Running   1          30m

[root@node1 deploy-k8s1]# kubectl describe pod busybox-f54ff6946-2t86r |grep -i container
Containers:
    Container ID:  containerd://d593a291f95691bf7a119d145bd1fa87cb588790a422c3729ff3d19c5d3b5559
  ContainersReady   True
[root@node1 deploy-k8s1]# crictl inspect d593a291f95691bf7a119d145bd1fa87cb588790a422c3729ff3d19c5d3b5559 |grep -i runtime
    "runtimeType": "io.containerd.runc.v2",
    "runtimeOptions": null,
    "runtimeSpec": {
[root@node1 deploy-k8s1]# kubectl describe pod kata-nginx |grep -i container
Containers:
    Container ID:   containerd://2771f2ccf03355c827aa19fd039b4c03784ec6394a92f2488ddf304de4a0b49e
  ContainersReady   True
  Normal  Pulled     13m   kubelet            Container image "nginx:1.19" already present on machine
  Normal  Created    13m   kubelet            Created container nginx
  Normal  Started    13m   kubelet            Started container nginx
[root@node1 deploy-k8s1]# crictl inspect 2771f2ccf03355c827aa19fd039b4c03784ec6394a92f2488ddf304de4a0b49e |grep -i runtime
    "runtimeType": "io.containerd.kata.v2",
    "runtimeOptions": null,
    "runtimeSpec": {