

# 用一个ISO来安装kubevirt VM 

https://kubevirt.io/2020/KubeVirt-installing_Microsoft_Windows_from_an_iso.html





# kubevirt 升级



https://kubevirt.io/2020/KubeVirt-Operation-Fundamentals.html

https://kubevirt.io/user-guide/#/installation/updating-and-deleting-installs



**Step 1.** Update virt-operator from our original install of v0.27.0 to v0.28.0 by applying a new virt-operator manifest.

```
kubectl apply -f https://github.com/kubevirt/kubevirt/releases/download/v0.28.0/kubevirt-operator.yaml
```

**Step 2:** Watch the install object to see when the installation completes. Eventually it will report v0.28.0 as the observed version which indicates the update has completed.

Copy

```
kubectl get kv -o yaml -n kubevirt | grep observedKubeVirtVersion
```



待测试： 按道理应该不会影响vm的正常运行。

# kubevirt 安全



https://kubevirt.io/2020/KubeVirt-Security-Fundamentals.html





# virtctl image-upload

virtctl.exe image-upload dv testvm --size=14Gi --image-path="C:\Users\Administrator\VirtualBox VMs\testvm\testvm.vdi"





# kvm虚拟机迁移

从oVirt迁移到Kubevirt

https://kubevirt.io/2020/Import-VM-from-oVirt.html



从Vmware迁移到Kubevirt 

直接对接vcenter

https://github.com/kubevirt/containerized-data-importer/blob/master/doc/datavolumes.md#vddk-data-volume





# DataVolume 空的，tar 



https://github.com/kubevirt/containerized-data-importer/blob/master/doc/datavolumes.md#vddk-data-volume



```
apiVersion: cdi.kubevirt.io/v1beta1
kind: DataVolume
metadata:
  name: "example-import-dv"
spec:
  source:
      http:
         url: "http://server/archive.tar"
         secretRef: "" # Optional
   contentType: "archive"
  pvc:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: "64Mi"
```



```
apiVersion: cdi.kubevirt.io/v1beta1
kind: DataVolume
metadata:
  name: example-upload-dv
spec:
  source:
    upload: {}
  pvc:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 1Gi
```





```
apiVersion: cdi.kubevirt.io/v1beta1
kind: DataVolume
metadata:
  name: example-blank-dv
spec:
  source:
    blank: {}
  pvc:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 1Gi
```

```
apiVersion: cdi.kubevirt.io/v1beta1
kind: DataVolume
metadata:
  name: import-block-pv-datavolume
spec:
  # Optional: Set the storage class or omit to accept the default
  storageClassName: local
  source:
      http:
         url: "http://distro.ibiblio.org/tinycorelinux/9.x/x86/release/Core-current.iso"
  pvc:
    volumeMode: Block
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 1Gi
```

