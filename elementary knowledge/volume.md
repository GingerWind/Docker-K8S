volume

支持的类型：

```
emptyDir
hostPath
GCE Persistent Disk
AWS Elastic Block Stoasdfasdfre
NFS
CEPH
...
```



emptyDir

生命周期与pod一致。宿主机运行pod对应的docker进程挂载的目录

```
volumes:
- name: shared-volume
  emptyDir: {}
```



hostPath

是将节点存在的目录mount给pod的容器

```
volumes:
- hostPath:
    path: /etc/kubernetes
  name: k8s
```



外部storage provider

```
volumes:
- name: ebs-volume
  awsElasticBlockStore:
    volumeID: <volume-id>
    fsType: ext4
```



```
volumes:
- name: ceph-volume
  cephfs:
    path: /some/path/in/side/cephfs
    monitors: "10.16.154.78:6789"
    secretFile: "/etc/ceph/admin.secret"
```



PersistenVolume(PV)

外部存储系统中的一块存储空间，管理员创建和维护

PersistenVolumeClain(PVC)

对PV的申请(Claim)。普通用户创建和维护

```
apiVersion: v1
kind: PersistentVolume
metadata:
    name: pv-evs-example
spec:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 10Gi
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: nfs
  nfs:
    path: /nfsdata/pv1
    server: 192.168.56.105
```



 `persistentVolumeReclaimPolicy` 指定当 PV 的回收策略为 `Recycle`，支持的策略有：
Retain – 需要管理员手工回收。
Recycle – 清除 PV 中的数据，效果相当于执行 `rm -rf /thevolume/*`。
Delete – 删除 Storage Provider 上的对应存储资源，例如 AWS EBS、GCE PD、Azure Disk、OpenStack Cinder Volume 等



```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
   name: pvc-evs-example
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName:  nfs
```



在 `volumes` 中通过 `persistentVolumeClaim` 指定使用 `mypvc1` 申请的 Volume。



当pv不再需要时，可通过pvc回收

```
kubectl delete pvc mypvc1
```



pv动态供给(Dynamical Provision)

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: standard
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
reclaimPolicy: Retain
```





pod中使用pvc时

```
template:
  ...
  
    volumeMounts:
    - name: mysql-persistente-storage # volume name
      mountPath: /var/lib/mysql
  volumes:
  - name: mysql-persistente-storage
    persistentVolumeClaim:
      claimName: mysql-pvc  #pvc name
```

