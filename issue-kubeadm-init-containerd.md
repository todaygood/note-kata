# Issue:containerd 使用kata,然后kubeadm init 失败



kubeadm init 时使用containerd ，出现问题





在containerd 正确配置之后，依然出现这个问题：

```
[root@node1 deploy-k8s1]# kubeadm init --config=kubeadm-config-containerd.yaml --upload-certs
W1221 10:42:03.595293   11389 configset.go:348] WARNING: kubeadm cannot validate component configs for API groups [kubelet.config.k8s.io kubeproxy.config.k8s.io]
[init] Using Kubernetes version: v1.19.2
[preflight] Running pre-flight checks
        [WARNING Firewalld]: firewalld is active, please ensure ports [6443 10250] are open or your cluster may not function correctly
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local node1] and IPs [10.96.0.1 192.168.122.1 192.168.5.8]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [localhost node1] and IPs [192.168.122.1 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [localhost node1] and IPs [192.168.122.1 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[kubelet-check] Initial timeout of 40s passed.
[kubelet-check] It seems like the kubelet isn't running or healthy.
[kubelet-check] The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get "http://localhost:10248/healthz": dial tcp 127.0.0.1:10248: connect: connection refused.
[kubelet-check] It seems like the kubelet isn't running or healthy.
[kubelet-check] The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get "http://localhost:10248/healthz": dial tcp 127.0.0.1:10248: connect: connection refused.
[kubelet-check] It seems like the kubelet isn't running or healthy.
[kubelet-check] The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get "http://localhost:10248/healthz": dial tcp 127.0.0.1:10248: connect: connection refused.
[kubelet-check] It seems like the kubelet isn't running or healthy.
[kubelet-check] The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get "http://localhost:10248/healthz": dial tcp 127.0.0.1:10248: connect: connection refused.
[kubelet-check] It seems like the kubelet isn't running or healthy.
[kubelet-check] The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get "http://localhost:10248/healthz": dial tcp 127.0.0.1:10248: connect: connection refused.

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

ps 查看kubelet 没有起来。 



ExecStart=/usr/bin/kubelet --container-runtime=remote  --container-runtime-endpoint=/run/containerd/containerd.sock  --config /etc/kubelet/config.yaml

kubelet日志如下：意思是没有使用 ca 证书。 

```
Dec 21 14:33:55 node1 systemd[1]: Started kubelet: The Kubernetes Node Agent.
Dec 21 14:33:55 node1 kubelet[28009]: I1221 14:33:55.965939   28009 server.go:411] Version: v1.19.2
Dec 21 14:33:55 node1 kubelet[28009]: W1221 14:33:55.966369   28009 server.go:553] standalone mode, no API client
Dec 21 14:33:55 node1 kubelet[28009]: F1221 14:33:55.966391   28009 server.go:265] failed to run Kubelet: no client provided, cannot use webhook authentication
Dec 21 14:33:55 node1 kubelet[28009]: goroutine 1 [running]:
Dec 21 14:33:55 node1 kubelet[28009]: k8s.io/kubernetes/vendor/k8s.io/klog/v2.stacks(0xc000010001, 0xc00066c3c0, 0x7a, 0x9e)
Dec 21 14:33:55 node1 kubelet[28009]: /workspace/anago-v1.19.2-rc.0.12+19706d90d87784/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/k8s.io/klog/v2/klog.go:996 +0xb9
Dec 21 14:33:55 node1 kubelet[28009]: k8s.io/kubernetes/vendor/k8s.io/klog/v2.(*loggingT).output(0x6cf6140, 0xc000000003, 0x0, 0x0, 0xc00088da40, 0x6b49c19, 0x9, 0x109, 0x0)
Dec 21 14:33:55 node1 kubelet[28009]: /workspace/anago-v1.19.2-rc.0.12+19706d90d87784/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/k8s.io/klog/v2/klog.go:945 +0x191
Dec 21 14:33:55 node1 kubelet[28009]: k8s.io/kubernetes/vendor/k8s.io/klog/v2.(*loggingT).printDepth(0x6cf6140, 0xc000000003, 0x0, 0x0, 0x1, 0xc000a3fc80, 0x1, 0x1)
Dec 21 14:33:55 node1 kubelet[28009]: /workspace/anago-v1.19.2-rc.0.12+19706d90d87784/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/k8s.io/klog/v2/klog.go:718 +0x165
Dec 21 14:33:55 node1 kubelet[28009]: k8s.io/kubernetes/vendor/k8s.io/klog/v2.(*loggingT).print(...)
Dec 21 14:33:55 node1 kubelet[28009]: /workspace/anago-v1.19.2-rc.0.12+19706d90d87784/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/k8s.io/klog/v2/klog.go:703
Dec 21 14:33:55 node1 kubelet[28009]: k8s.io/kubernetes/vendor/k8s.io/klog/v2.Fatal(...)
Dec 21 14:33:55 node1 kubelet[28009]: /workspace/anago-v1.19.2-rc.0.12+19706d90d87784/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/k8s.io/klog/v2/klog.go:1436
Dec 21 14:33:55 node1 kubelet[28009]: k8s.io/kubernetes/cmd/kubelet/app.NewKubeletCommand.func1(0xc0000fa000, 0xc00004e060, 0x4, 0x4)
Dec 21 14:33:55 node1 kubelet[28009]: /workspace/anago-v1.19.2-rc.0.12+19706d90d87784/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/cmd/kubelet/app/server.go:265 +0x63e
Dec 21 14:33:55 node1 kubelet[28009]: k8s.io/kubernetes/vendor/github.com/spf13/cobra.(*Command).execute(0xc0000fa000, 0xc00004e060, 0x4, 0x4, 0xc0000fa000, 0xc00004e060)
Dec 21 14:33:55 node1 kubelet[28009]: /workspace/anago-v1.19.2-rc.0.12+19706d90d87784/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/github.com/spf13/cobra/command.go:846 +0x2c2
Dec 21 14:33:55 node1 kubelet[28009]: k8s.io/kubernetes/vendor/github.com/spf13/cobra.(*Command).ExecuteC(0xc0000fa000, 0x1652a7ba66b9f755, 0x6cf5c60, 0x409b05)
Dec 21 14:33:55 node1 kubelet[28009]: /workspace/anago-v1.19.2-rc.0.12+19706d90d87784/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/github.com/spf13/cobra/command.go:950 +0x375
Dec 21 14:33:55 node1 kubelet[28009]: k8s.io/kubernetes/vendor/github.com/spf13/cobra.(*Command).Execute(...)
Dec 21 14:33:55 node1 kubelet[28009]: /workspace/anago-v1.19.2-rc.0.12+19706d90d87784/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/github.com/spf13/cobra/command.go:887
Dec 21 14:33:55 node1 kubelet[28009]: main.main()
Dec 21 14:33:55 node1 kubelet[28009]: _output/dockerized/go/src/k8s.io/kubernetes/cmd/kubelet/kubelet.go:41 +0xe5
Dec 21 14:33:55 node1 kubelet[28009]: goroutine 4 [chan receive]:
Dec 21 14:33:55 node1 kubelet[28009]: k8s.io/kubernetes/vendor/k8s.io/klog/v2.(*loggingT).flushDaemon(0x6cf6140)
Dec 21 14:33:55 node1 kubelet[28009]: /workspace/anago-v1.19.2-rc.0.12+19706d90d87784/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/k8s.io/klog/v2/klog.go:1131 +0x8b
Dec 21 14:33:55 node1 kubelet[28009]: created by k8s.io/kubernetes/vendor/k8s.io/klog/v2.init.0

```









## 进展1

把  --config /etc/kubelet/config.yaml 参数去掉之后，就不出现“failed to run Kubelet: no client provided, cannot use webhook authentication"错误了，而且

[kubelet-check] The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get "http://localhost:10248/healthz": dial tcp 127.0.0.1:10248: connect: connection refused.

这个也没有出现了。



[root@node1 deploy-k8s1]# ps aux |grep kubelet
root     30474  2.3  2.0 1306864 81656 ?       Ssl  15:11   0:03 /usr/bin/kubelet --container-runtime=remote --container-runtime-endpoint=unix:///run/containerd/containerd.sock
root     30554  0.0  0.0 112824  2304 pts/3    S+   15:14   0:00 grep --color=auto kubelet



```
[certs] Generating "apiserver-kubelet-client" certificate and key
I1221 15:20:50.443816   30704 certs.go:103] creating a new certificate authority for front-proxy-ca
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
I1221 15:20:51.566620   30704 certs.go:103] creating a new certificate authority for etcd-ca
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [localhost node1] and IPs [192.168.122.1 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [localhost node1] and IPs [192.168.122.1 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
I1221 15:20:54.006093   30704 certs.go:69] creating new public/private key files for signing service account users
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
I1221 15:20:54.486196   30704 kubeconfig.go:84] creating kubeconfig file for admin.conf
[kubeconfig] Writing "admin.conf" kubeconfig file
I1221 15:20:54.892949   30704 kubeconfig.go:84] creating kubeconfig file for kubelet.conf
[kubeconfig] Writing "kubelet.conf" kubeconfig file
I1221 15:20:55.252838   30704 kubeconfig.go:84] creating kubeconfig file for controller-manager.conf
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
I1221 15:20:55.739808   30704 kubeconfig.go:84] creating kubeconfig file for scheduler.conf
[kubeconfig] Writing "scheduler.conf" kubeconfig file
I1221 15:20:56.127106   30704 kubelet.go:63] Stopping the kubelet
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
I1221 15:20:56.339737   30704 manifests.go:96] [control-plane] getting StaticPodSpecs
I1221 15:20:56.340353   30704 manifests.go:109] [control-plane] adding volume "ca-certs" for component "kube-apiserver"
I1221 15:20:56.340368   30704 manifests.go:109] [control-plane] adding volume "etc-pki" for component "kube-apiserver"
I1221 15:20:56.340380   30704 manifests.go:109] [control-plane] adding volume "k8s-certs" for component "kube-apiserver"
I1221 15:20:56.349852   30704 manifests.go:135] [control-plane] wrote static Pod manifest for component "kube-apiserver" to "/etc/kubernetes/manifests/kube-apiserver.yaml"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
I1221 15:20:56.349904   30704 manifests.go:96] [control-plane] getting StaticPodSpecs
I1221 15:20:56.350292   30704 manifests.go:109] [control-plane] adding volume "ca-certs" for component "kube-controller-manager"
I1221 15:20:56.350313   30704 manifests.go:109] [control-plane] adding volume "etc-pki" for component "kube-controller-manager"
I1221 15:20:56.350329   30704 manifests.go:109] [control-plane] adding volume "flexvolume-dir" for component "kube-controller-manager"
I1221 15:20:56.350345   30704 manifests.go:109] [control-plane] adding volume "k8s-certs" for component "kube-controller-manager"
I1221 15:20:56.350361   30704 manifests.go:109] [control-plane] adding volume "kubeconfig" for component "kube-controller-manager"
I1221 15:20:56.355397   30704 manifests.go:135] [control-plane] wrote static Pod manifest for component "kube-controller-manager" to "/etc/kubernetes/manifests/kube-controller-manager.yaml"
[control-plane] Creating static Pod manifest for "kube-scheduler"
I1221 15:20:56.355572   30704 manifests.go:96] [control-plane] getting StaticPodSpecs
I1221 15:20:56.356147   30704 manifests.go:109] [control-plane] adding volume "kubeconfig" for component "kube-scheduler"
I1221 15:20:56.357318   30704 manifests.go:135] [control-plane] wrote static Pod manifest for component "kube-scheduler" to "/etc/kubernetes/manifests/kube-scheduler.yaml"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
I1221 15:20:56.359328   30704 local.go:82] [etcd] wrote Static Pod manifest for a local etcd member to "/etc/kubernetes/manifests/etcd.yaml"
I1221 15:20:56.359412   30704 waitcontrolplane.go:87] [wait-control-plane] Waiting for the API server to be healthy
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
                - 'crictl --runtime-endpoint unix:///run/containerd/containerd.sock ps -a | grep kube | grep -v pause'
                Once you have found the failing container, you can inspect its logs with:
                - 'crictl --runtime-endpoint unix:///run/containerd/containerd.sock logs CONTAINERID'


```



kubelet 日志：



```
[root@node1 deploy-k8s1]# ps aux |grep -i kubelet
root     30806  3.3  2.0 1241584 82568 ?       Ssl  15:20   0:02 /usr/bin/kubelet --container-runtime=remote --container-runtime-endpoint=unix:///run/containerd/containerd.sock
root     30873  0.0  0.0 112824  2300 pts/3    S+   15:21   0:00 grep --color=auto -i kubelet

```



```
Dec 21 15:19:44 node1 systemd[1]: Stopped kubelet: The Kubernetes Node Agent.
Dec 21 15:20:56 node1 systemd[1]: Started kubelet: The Kubernetes Node Agent.
Dec 21 15:20:57 node1 kubelet[30806]: I1221 15:20:57.187390   30806 server.go:411] Version: v1.19.2
Dec 21 15:20:57 node1 kubelet[30806]: W1221 15:20:57.187856   30806 server.go:553] standalone mode, no API client
Dec 21 15:21:02 node1 kubelet[30806]: W1221 15:21:02.196917   30806 nvidia.go:61] NVIDIA GPU metrics will not be available: no NVIDIA devices found
Dec 21 15:21:02 node1 kubelet[30806]: W1221 15:21:02.220491   30806 server.go:468] No api server defined - no events will be sent to API server.
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.220571   30806 server.go:640] --cgroups-per-qos enabled, but --cgroup-root was not specified.  defaulting to /
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.221858   30806 container_manager_linux.go:276] container manager verified user specified cgroup-root exists: []
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.221927   30806 container_manager_linux.go:281] Creating Container Manager object based on Node Config: {RuntimeCgroupsName: SystemCgroupsName: KubeletCgroupsName: ContainerRuntime:remote CgroupsPerQOS:true CgroupRoot:/ CgroupDriver:cgroupfs KubeletRootDir:/var/lib/kubelet ProtectKernelDefaults:false NodeAllocatableConfig:{KubeReservedCgroupName: SystemReservedCgroupName: ReservedSystemCPUs: EnforceNodeAllocatable:map[pods:{}] KubeReserved:map[] SystemReserved:map[] HardEvictionThresholds:[{Signal:memory.available Operator:LessThan Value:{Quantity:100Mi Percentage:0} GracePeriod:0s MinReclaim:<nil>} {Signal:nodefs.available Operator:LessThan Value:{Quantity:<nil> Percentage:0.1} GracePeriod:0s MinReclaim:<nil>} {Signal:nodefs.inodesFree Operator:LessThan Value:{Quantity:<nil> Percentage:0.05} GracePeriod:0s MinReclaim:<nil>} {Signal:imagefs.available Operator:LessThan Value:{Quantity:<nil> Percentage:0.15} GracePeriod:0s MinReclaim:<nil>}]} QOSReserved:map[] ExperimentalCPUManagerPolicy:none ExperimentalCPUManagerReconcilePeriod:10s ExperimentalPodPidsLimit:-1 EnforceCPULimits:true CPUCFSQuotaPeriod:100ms ExperimentalTopologyManagerPolicy:none}
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.222344   30806 topology_manager.go:126] [topologymanager] Creating topology manager with none policy
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.222389   30806 container_manager_linux.go:311] [topologymanager] Initializing Topology Manager with none policy
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.222424   30806 container_manager_linux.go:316] Creating device plugin manager: true
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.222907   30806 remote_runtime.go:59] parsed scheme: ""
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.222956   30806 remote_runtime.go:59] scheme "" not registered, fallback to default scheme
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.223072   30806 passthrough.go:48] ccResolverWrapper: sending update to cc: {[{/run/containerd/containerd.sock  <nil> 0 <nil>}] <nil> <nil>}
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.223116   30806 clientconn.go:948] ClientConn switching balancer to "pick_first"
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.223264   30806 remote_image.go:50] parsed scheme: ""

Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.223116   30806 clientconn.go:948] ClientConn switching balancer to "pick_first"
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.223264   30806 remote_image.go:50] parsed scheme: ""
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.223322   30806 remote_image.go:50] scheme "" not registered, fallback to default scheme
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.223376   30806 passthrough.go:48] ccResolverWrapper: sending update to cc: {[{/run/containerd/containerd.sock  <nil> 0 <nil>}] <nil> <nil>}
Dec 21 15:21:02 node1 kubelet[30806]: I1221 15:21:02.223414   30806 clientconn.go:948] ClientConn switching balancer to "pick_first"
Dec 21 15:21:08 node1 kubelet[30806]: E1221 15:21:08.571325   30806 aws_credentials.go:77] while getting AWS credentials NoCredentialProviders: no valid providers in chain. Deprecated.
Dec 21 15:21:08 node1 kubelet[30806]: For verbose messaging see aws.Config.CredentialsChainVerboseErrors
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.574082   30806 kuberuntime_manager.go:214] Container runtime containerd initialized, version: v1.4.3, apiVersion: v1alpha2
Dec 21 15:21:08 node1 kubelet[30806]: W1221 15:21:08.574803   30806 volume_host.go:74] kubeClient is nil. Skip initialization of CSIDriverLister
Dec 21 15:21:08 node1 kubelet[30806]: W1221 15:21:08.577009   30806 csi_plugin.go:188] kubernetes.io/csi: kubeclient not set, assuming standalone kubelet
Dec 21 15:21:08 node1 kubelet[30806]: W1221 15:21:08.577072   30806 csi_plugin.go:258] Skipping CSINode initialization, kubelet running in standalone mode
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.577665   30806 server.go:1147] Started kubelet
Dec 21 15:21:08 node1 kubelet[30806]: W1221 15:21:08.578377   30806 kubelet.go:1319] No api server defined - no node status update will be sent.
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.582335   30806 fs_resource_analyzer.go:64] Starting FS ResourceAnalyzer
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.582379   30806 server.go:152] Starting to listen on 0.0.0.0:10250
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.588037   30806 volume_manager.go:265] Starting Kubelet Volume Manager
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.589559   30806 desired_state_of_world_populator.go:139] Desired state populator starts to run
Dec 21 15:21:08 node1 kubelet[30806]: E1221 15:21:08.590154   30806 cri_stats_provider.go:376] Failed to get the info of the filesystem with mountpoint "/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs": unable to find data in memory cache.
Dec 21 15:21:08 node1 kubelet[30806]: E1221 15:21:08.590212   30806 kubelet.go:1218] Image garbage collection failed once. Stats initialization may not have completed yet: invalid capacity 0 on image filesystem
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.599975   30806 server.go:424] Adding debug handlers to kubelet server.
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.612917   30806 client.go:87] parsed scheme: "unix"
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.612994   30806 client.go:87] scheme "unix" not registered, fallback to default scheme
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.613350   30806 passthrough.go:48] ccResolverWrapper: sending update to cc: {[{unix:///run/containerd/containerd.sock  <nil> 0 <nil>}] <nil> <nil>}
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.613403   30806 clientconn.go:948] ClientConn switching balancer to "pick_first"
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.685478   30806 status_manager.go:154] Kubernetes client is nil, not starting status manager.
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.685512   30806 kubelet.go:1741] Starting kubelet main sync loop.
Dec 21 15:21:08 node1 kubelet[30806]: E1221 15:21:08.685647   30806 kubelet.go:1765] skipping pod synchronization - [container runtime status check may not have completed yet, PLEG is not healthy: pleg has yet to be successful]
Dec 21 15:21:08 node1 kubelet[30806]: E1221 15:21:08.785705   30806 kubelet.go:1765] skipping pod synchronization - container runtime status check may not have completed yet
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.788123   30806 cpu_manager.go:184] [cpumanager] starting with none policy
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.788139   30806 cpu_manager.go:185] [cpumanager] reconciling every 10s
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.788161   30806 state_mem.go:36] [cpumanager] initializing new in-memory state store
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.790015   30806 reconciler.go:157] Reconciler: start to sync state
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.791997   30806 policy_none.go:43] [cpumanager] none policy: Start
Dec 21 15:21:08 node1 kubelet[30806]: W1221 15:21:08.792999   30806 manager.go:596] Failed to retrieve checkpoint for "kubelet_internal_checkpoint": checkpoint is not found
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.793617   30806 plugin_manager.go:114] Starting Kubelet Plugin Manager
Dec 21 15:21:08 node1 kubelet[30806]: I1221 15:21:08.793617   30806 plugin_manager.go:114] Starting Kubelet Plugin Manager
```



没有组件启动

```
[root@node1 deploy-k8s1]# crictl --runtime-endpoint unix:///run/containerd/containerd.sock ps -a
CONTAINER           IMAGE               CREATED             STATE               NAME                ATTEMPT             POD ID
```

