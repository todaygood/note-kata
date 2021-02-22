# How to use kata?

## 方式1：使用 `untrusted_workload_runtime` 的方式

对于不符合上述版本要求的环境，可以使用之前的方式。

在配置文件中新增如下内容：

```
    [plugins.cri.containerd.untrusted_workload_runtime]
      runtime_type = "io.containerd.runtime.v1.linux"
      runtime_engine = "/usr/bin/kata-runtime"
```

最后，都需要重启 containerd。

```
containerd systemctl daemon-reload
systemctl restart containerd
```

K8s 资源需要增加 annotation

```
apiVersion: v1
kind: Pod
metadata:
  name: kata-nginx-untrusted
  annotations:
    io.kubernetes.cri.untrusted-workload: "true"
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```



## 方式2：使用kata作为runtime 

创建RuntimeClass

参见：https://ignorantshr.github.io/person-blog/kata-containers/k8s+containerd+kata-containers%20%E5%AE%89%E8%A3%85/ 



这里 `[plugins.cri.containerd.runtimes.kata]` 中的 kata 将被作为 RuntimeClass handler 关键字。




kubectl apply -f runtimeclass.yml

在Pod中指定RuntimeClass

```
[root@manager yml]# cat nginx-deployment.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      test: nginx
  template:
    metadata:
      labels:
        test: nginx
    spec:
      runtimeClassName: kataclass
      containers:
      - name: nginx
        image: nginx
```

查看负载

kata-runtime list





