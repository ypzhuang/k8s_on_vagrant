# Install K8S Cluster on Vagrant


Tested on  Mac with [vagrant](https://www.vagrantup.com/downloads.html) 2.2.5 and  [VirtualBox](https://www.virtualbox.org/wiki/Downloads) 6.0.12

## Start up, may take a long time to download

```
vagrant up
vagrant ssh u1  # login k8s master node
kubectl get pods --all-namespaces
```

## Optional
if in China, and have never installed a vagrant box of ubuntu, you can first install the box from a China mirror to speed up downloading:

For the box of ubuntu/bionic64(18.04):
```
vagrant box add https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cloud-images/bionic/current/bionic-server-cloudimg-amd64-vagrant.box --name ubuntu/bionic64
```

For the box of ubuntu/xenial64(16.04):
```
vagrant box add https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cloud-images/xenial/current/xenial-server-cloudimg-amd64-vagrant.box --name ubuntu/xenial64
```

## Change default config in Vagrantfile(Optional)
```
NODE_VCPUS = 2 #number of cpu
NODE_MASTER_MEMORY_SIZE = 2048 # memory size of master node(MB)
NODE_WORKER_MEMORY_SIZE = 2048 # memory size of worker nodes(MB)
NODES = 3 # number of nodes, you can set 1 only for a master node
IN_CHINA = 'yes' # yes or no,  yes: who who cannot connect to Google K8S source.
UBUNTU_RELEASE = "ubuntu/bionic64"  # ubuntu/xenial64:16.04 ubuntu/bionic64 18.04
```
## VM List


| IP | Role | Hostname |
| :-----| :----- | :-----|
| 192.168.9.11 | manager | u1|
| 192.168.9.12 | worker  | u2|
| 192.168.9.13 | worker  | u3|
| .... | worker  | ..|

## Version Info

### kubernetes version: 1.16.2
```
 kubectl version
Client Version: version.Info{Major:"1", Minor:"16", GitVersion:"v1.16.2", GitCommit:"c97fe5036ef3df2967d086711e6c0c405941e14b", GitTreeState:"clean", BuildDate:"2019-10-15T19:18:23Z", GoVersion:"go1.12.10", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"16", GitVersion:"v1.16.2", GitCommit:"c97fe5036ef3df2967d086711e6c0c405941e14b", GitTreeState:"clean", BuildDate:"2019-10-15T19:09:08Z", GoVersion:"go1.12.10", Compiler:"gc", Platform:"linux/amd64"}
```

### docker version: 18.09.9
```
docker version
Client:
 Version:           18.09.9
 API version:       1.39
 Go version:        go1.11.13
 Git commit:        039a7df9ba
 Built:             Wed Sep  4 16:57:28 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.9
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.11.13
  Git commit:       039a7df
  Built:            Wed Sep  4 16:19:38 2019
  OS/Arch:          linux/amd64
  Experimental:     false
```


### images 1.0:
```
docker images
REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
calico/node                          v3.8.4              83b416d24205        7 days ago          191MB
calico/pod2daemon-flexvol            v3.8.4              207f157c99ac        7 days ago          9.37MB
calico/cni                           v3.8.4              20d7eefd5ce2        7 days ago          157MB
k8s.gcr.io/kube-proxy                v1.16.2             8454cbe08dc9        9 days ago          86.1MB
k8s.gcr.io/kube-scheduler            v1.16.2             ebac1ae204a2        9 days ago          87.3MB
k8s.gcr.io/kube-apiserver            v1.16.2             c2c9a0406787        9 days ago          217MB
k8s.gcr.io/kube-controller-manager   v1.16.2             6e4bffa46d70        9 days ago          163MB
k8s.gcr.io/etcd                      3.3.15-0            b2756210eeab        7 weeks ago         247MB
k8s.gcr.io/coredns                   1.6.2               bf261d157914        2 months ago        44.1MB
quay.io/coreos/flannel               v0.11.0             ff281650a721        8 months ago        52.6MB
k8s.gcr.io/pause                     3.1                 da86e6ba6ca1        22 months ago       742kB
```
