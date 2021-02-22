

```
[root@node1 golang-release]# kata-runtime kata-check
INFO[0000] CPU property found                            arch=amd64 description="Intel Architecture CPU" name=GenuineIntel pid=3871 source=runtime type=attribute
INFO[0000] CPU property found                            arch=amd64 description="Virtualization support" name=vmx pid=3871 source=runtime type=flag
INFO[0000] CPU property found                            arch=amd64 description="64Bit CPU" name=lm pid=3871 source=runtime type=flag
INFO[0000] CPU property found                            arch=amd64 description=SSE4.1 name=sse4_1 pid=3871 source=runtime type=flag
INFO[0000] kernel property found                         arch=amd64 description="Host Support for Linux VM Sockets" name=vhost_vsock pid=3871 source=runtime type=module
INFO[0000] kernel property found                         arch=amd64 description="Intel KVM" name=kvm_intel pid=3871 source=runtime type=module
INFO[0000] kernel property found                         arch=amd64 description="Kernel-based Virtual Machine" name=kvm pid=3871 source=runtime type=module
INFO[0000] kernel property found                         arch=amd64 description="Host kernel accelerator for virtio" name=vhost pid=3871 source=runtime type=module
INFO[0000] kernel property found                         arch=amd64 description="Host kernel accelerator for virtio network" name=vhost_net pid=3871 source=runtime type=module
INFO[0000] System is capable of running Kata Containers  arch=amd64 name=kata-runtime pid=3871 source=runtime
INFO[0000] device available                              arch=amd64 check-type=full device=/dev/kvm name=kata-runtime pid=3871 source=runtime
INFO[0000] feature available                             arch=amd64 check-type=full feature=create-vm name=kata-runtime pid=3871 source=runtime
INFO[0000] System can currently create Kata Containers   arch=amd64 name=kata-runtime pid=3871 source=runtime
```

# 启动一个kata容器之后的分析

启动一个kata容器之后,有qemu进程

同时看到 
- containerd-shim， 这个是调用kata-runtime的父进程。
- kata-proxy 
- kata-shim 
等这三个进程。 



```
[root@node1 kata0.git]# ps aux |grep kata
root      9127  0.1  0.1 109032  6704 ?        Sl   15:14   0:00 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368 -address /run/containerd/containerd.sock -containerd-binary /usr/bin/containerd -runtime-root /var/run/docker/runtime-kata-runtime


root      9171 15.5  4.2 2944736 162828 ?      Sl   15:14   0:05 /usr/bin/qemu-vanilla-system-x86_64
 -name sandbox-3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368 
 -uuid 855d6d71-b374-46c3-8d54-9f5b16876cba -machine pc,accel=kvm,kernel_irqchip,nvdimm 
 -cpu host -qmp unix:/run/vc/vm/3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368/qmp.sock,server,nowait 
 -m 2048M,slots=10,maxmem=4743M -device pci-bridge,bus=pci.0,id=pci-bridge-0,chassis_nr=1,shpc=on,addr=2,romfile= -device virtio-serial-pci,disable-modern=false,id=serial0,romfile= -device virtconsole,chardev=charconsole0,id=console0
 
 -chardev socket,id=charconsole0,path=/run/vc/vm/3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368/console.sock,server,nowait -device nvdimm,id=nv0,memdev=mem0 
 -object memory-backend-file,id=mem0,mem-path=/usr/share/kata-containers/kata-containers-image_clearlinux_1.7.3_agent_4f912b5c35.img,size=134217728 -device virtio-scsi-pci,id=scsi0,disable-modern=false,romfile= 
 
 -object rng-random,id=rng0,filename=/dev/urandom -device virtio-rng,rng=rng0,romfile= -device virtserialport,chardev=charch0,id=channel0,name=agent.channel.0 
 
 -chardev socket,id=charch0,path=/run/vc/vm/3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368/kata.sock,server,nowait 
 
 -device virtio-9p-pci,disable-modern=false,fsdev=extra-9p-kataShared,mount_tag=kataShared,romfile= 
 
 -fsdev local,id=extra-9p-kataShared,path=/run/kata-containers/shared/sandboxes/3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368,security_model=none 
 
 -netdev tap,id=network-0,vhost=on,vhostfds=3,fds=4 
 
 -device driver=virtio-net-pci,netdev=network-0,mac=02:42:ac:11:00:03,disable-modern=false,mq=on,vectors=4,romfile= -global kvm-pit.lost_tick_policy=discard 
 -vga none -no-user-config -nodefaults -nographic -daemonize 
 -kernel /usr/share/kata-containers/vmlinuz-4.19.52.44-4.4.container 
 -append tsc=reliable no_timer_check rcupdate.rcu_expedited=1 i8042.direct=1 i8042.dumbkbd=1 i8042.nopnp=1 i8042.noaux=1 noreplace-smp reboot=k console=hvc0 console=hvc1 iommu=off cryptomgr.notests net.ifnames=0 pci=lastbus=0 
 root=/dev/pmem0p1 rootflags=dax,data=ordered,errors=remount-ro ro rootfstype=ext4 quiet systemd.show_status=false panic=1 nr_cpus=2 agent.use_vsock=false 
 
 init=/usr/lib/systemd/systemd systemd.unit=kata-containers.target systemd.mask=systemd-networkd.service systemd.mask=systemd-networkd.socket systemd.mask=systemd-journald.service systemd.mask=systemd-journald.socket systemd.mask=systemd-journal-flush.service systemd.mask=systemd-journald-dev-log.socket systemd.mask=systemd-udevd.service systemd.mask=systemd-udevd.socket systemd.mask=systemd-udev-trigger.service systemd.mask=systemd-udevd-kernel.socket systemd.mask=systemd-udevd-control.socket systemd.mask=systemd-timesyncd.service systemd.mask=systemd-update-utmp.service systemd.mask=systemd-tmpfiles-setup.service systemd.mask=systemd-tmpfiles-cleanup.service systemd.mask=systemd-tmpfiles-cleanup.timer systemd.mask=tmp.mount systemd.mask=systemd-random-seed.service systemd.mask=systemd-coredump@.service 
 -pidfile /run/vc/vm/3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368/pid -smp 1,cores=1,threads=1,sockets=2,maxcpus=2
 
 
 
 
 
root      9179  0.1  0.3 440816 11864 ?        Ssl  15:14   0:00 /usr/libexec/kata-containers/kata-proxy -listen-socket unix:///run/vc/sbs/3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368/proxy.sock -mux-socket /run/vc/vm/3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368/kata.sock -sandbox 3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368
root      9192  0.0  0.4 322632 18012 pts/3    Ssl+ 15:14   0:00 /usr/libexec/kata-containers/kata-shim -agent unix:///run/vc/sbs/3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368/proxy.sock -container 3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368 -exec-id 3b89b33c54eb1988767b90f65d3ffe20f0c2d51227392ecff7c52d90ad67b368 -terminal

```


使用 crictl 对 Kubernetes 节点进行调试
https://kubernetes.io/zh/docs/tasks/debug-application-cluster/crictl/




