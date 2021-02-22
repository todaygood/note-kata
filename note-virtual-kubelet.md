

# 扩展Kubernetes到无限｜Virtual Kubelet子项目tensile-kube介绍

https://blog.csdn.net/M2l0ZgSsVc7r69eFdTj/article/details/107273307?ops_request_misc=%25257B%252522request%25255Fid%252522%25253A%252522160913802516780266272654%252522%25252C%252522scm%252522%25253A%25252220140713.130102334.pc%25255Fall.%252522%25257D&request_id=160913802516780266272654&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_v2~rank_v29-5-107273307.first_rank_v2_pc_rank_v29&utm_term=virtual-kubelet%E4%BD%BF%E7%94%A8



目前VK社区已经将其独立出来，分为：virtual-kubelet、node-cli和provider。


virtual-kubelet提供了一种抽象能力，通过实现provider，用户就实现了一个虚拟的Kubernetes节点，用户的Pod可以调度到该节点。virtual-kubelet的核心功能是Node和Pod的生命周期管控。



![image-20201228202516311](C:\Source\todaygood\kata-note\image-20201228202516311.png)







