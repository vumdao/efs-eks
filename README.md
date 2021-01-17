<p align="center">
  <a href="https://dev.to/vumdao">
    <img alt="" src="https://dev-to-uploads.s3.amazonaws.com/i/2ekeineo8ut8gkcxlxxy.png" width="500" />
  </a>
</p>
<h1 align="center">
  <div><b>How to configure EKS Persistent Storage with EFS Amazon service for your Kubernetes cluster to use</b></div>
</h1>

## What’s In This Document 
- [Create EFS Volume](#-Create-EFS-Volume)
- [Create k8s resources to consume the EFS volume](#-Create-k8s-resources-to-consume-the-EFS-volume)

### 🚀 **[Create EFS Volume](#-Create-EFS-Volume)**

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/974op4ytkk8f2s13u541.png)

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/8s5euaxwjcgl23537h5l.png)

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/6eaxtrf2legdqr65mmoq.png)

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/8zbw7gn3azxmx85y49a2.png)

Get important elements after creating EFS
- File System ID:  fs-5cc93c7c
- DNS Name: fs-5cc93c7c.efs.us-east-1.amazonaws.com

### 🚀 **[Create k8s resources to consume the EFS volume](#-Create-k8s-resources-to-consume-the-EFS-volume)**
**Source: https://github.com/vumdao/efs-eks**

**2.1 Create EFS provisioner**
```
root@ctl:~/efs-eks-demo# kubectl apply -f efs-provisioner-deployment.yaml 
namespace/demo created
serviceaccount/efs-provisioner created
clusterrole.rbac.authorization.k8s.io/efs-provisioner created
clusterrolebinding.rbac.authorization.k8s.io/efs-provisioner created
role.rbac.authorization.k8s.io/leader-locking-efs-provisioner created
rolebinding.rbac.authorization.k8s.io/leader-locking-efs-provisioner created
deployment.apps/efs-provisioner created
configmap/efs-provisioner-config created

root@ctl:~/efs-eks-demo# kubectl get pod -n demo
NAME                               READY   STATUS    RESTARTS   AGE
efs-provisioner-84bbf9b5fc-zx5hs   1/1     Running   0          30s

root@ctl:~/efs-eks-demo# kubectl get configmap -n demo
NAME                     DATA   AGE
efs-provisioner-config   4      62s
```
**2.2 Create PVC**
```
root@ctl:~/efs-eks-demo# kubectl apply -f efs-pvc.yaml 
storageclass.storage.k8s.io/efs-sc created
persistentvolumeclaim/efs-demo-claim created

root@ctl:~/efs-eks-demo# kubectl get pvc -n demo
NAME             STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
efs-demo-claim   Bound    pvc-e00f83c9-4168-41ba-ae8b-08bc57f466e4   100Mi      RWX            aws-efs        10s

root@ctl:~/efs-eks-demo# kubectl get storageclass
NAME            PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
efs-sc          aws-efs                 Delete          Immediate           false                  27s
```

**2.3 Deploying the stateful services writer and reader the EFS volume**
```
root@ctl:~/efs-eks-demo# kubectl apply -f efs-writer.yaml 
pod/efs-writer created

root@ctl:~/efs-eks-demo# kubectl apply -f efs-reader.yaml 
pod/efs-reader created

root@ctl:~/efs-eks-demo# kubectl get pod -n demo
NAME                               READY   STATUS    RESTARTS   AGE
efs-provisioner-84bbf9b5fc-zx5hs   1/1     Running   0          3m6s
efs-reader                         1/1     Running   0          7s
efs-writer                         1/1     Running   0          14s
```

**2.4 Verify that the efs-reader pod is able to successfully read the same data from the shared persistent volume**
```
root@ctl:~/efs-eks-demo# kubectl exec -it efs-writer -n demo -- tail /shared/out.txt
efs-writer.demo - Sun Dec 13 16:59:04 UTC 2020
efs-writer.demo - Sun Dec 13 16:59:09 UTC 2020
efs-writer.demo - Sun Dec 13 16:59:14 UTC 2020
efs-writer.demo - Sun Dec 13 16:59:19 UTC 2020
efs-writer.demo - Sun Dec 13 16:59:24 UTC 2020

root@ctl:~/efs-eks-demo# kubectl exec -it efs-reader -n demo -- tail /shared/out.txt
efs-writer.demo - Sun Dec 13 16:59:04 UTC 2020
efs-writer.demo - Sun Dec 13 16:59:09 UTC 2020
efs-writer.demo - Sun Dec 13 16:59:14 UTC 2020
efs-writer.demo - Sun Dec 13 16:59:19 UTC 2020
efs-writer.demo - Sun Dec 13 16:59:24 UTC 2020
efs-writer.demo - Sun Dec 13 16:59:29 UTC 2020
```

### **Visit wwww.cloudopz.co to get more**


<h3 align="center">
  <a href="https://dev.to/vumdao">:stars: Blog</a>
  <span> · </span>
  <a href="https://vumdao.hashnode.dev/">Web</a>
  <span> · </span>
  <a href="https://www.linkedin.com/in/vu-dao-9280ab43/">Linkedin</a>
  <span> · </span>
  <a href="https://www.linkedin.com/groups/12488649/">Group</a>
  <span> · </span>
  <a href="https://www.facebook.com/CloudOpz-104917804863956">Page</a>
  <span> · </span>
  <a href="https://twitter.com/VuDao81124667">Twitter :stars:</a>
</h3>