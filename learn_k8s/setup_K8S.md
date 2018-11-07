# Set Up Kubernetes

## Installing kubeadm, kubelet and kubectl 

1. install etcd and docker
2. install kubernetes e.g. yum install kubelet-1.11.1 kubeadm-1.11.1  kubectl-1.11.1 kubernetes-cni

``` 
$vim  /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
gpgcheck=0
enable=1
```

3. stop  the firewall service

```
$ systemctl stop firewalld
$ systemctl disable firewalld
```

4. disable swap

```
$ sudo swapoff -a
$ sudo vi /etc/fstab ##comment /dev/mapper/centos-swap swap
$ vim /etc/sysctl.d/k8s.conf #add the following three lines
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1    
vm.swappiness=0
$ sysctl --system
$ vim /etc/sysconfig/kubelet
KUBELET_EXTRA_ARGS="--fail-swap-on=false"
```

5. initialize relevant services

```
$ systemctl enable docker
$ systemctl enable kubelet.service
$ systemctl start docker
$ systemctl start kubelet
```

6. modify kubernetes image repo

```
$ vim pullimages.sh #add the following lines
#!/bin/bash
images=(kube-proxy-amd64:v1.11.1 kube-scheduler-amd64:v1.11.1 kube-controller-manager-amd64:v1.11.1
kube-apiserver-amd64:v1.11.1 etcd-amd64:3.2.18 coredns:1.1.3 pause:3.1 )
for imageName in ${images[@]} ; do
docker pull anjia0532/google-containers.$imageName
docker tag anjia0532/google-containers.$imageName k8s.gcr.io/$imageName
docker rmi anjia0532/google-containers.$imageName
done
$ sh pullimages.sh #pulling the images
```



``` 
$ vim kubeadm.yaml
apiVersion: kubeadm.k8s.io/v1alpha1
kind: MasterConfiguration
controllerManagerExtraArgs:
  horizontal-pod-autoscaler-use-rest-clients: "true"
  horizontal-pod-autoscaler-sync-period: "10s"
  node-monitor-grace-period: "10s"
apiServerExtraArgs:
  runtime-config: "api/all=true"
kubernetesVersion: "v1.11.1"
```

- 

   <img src="https://static001.geekbang.org/resource/image/12/70/12a18c84cab19f79f981c2bf918d0270.jpg" width="400px" height="300px" > 

 