# Install K8S Cluster on Vagrant


Tested on  Mac with [vagrant](https://www.vagrantup.com/downloads.html) 2.2.5 and  [VirtualBox](https://www.virtualbox.org/wiki/Downloads) 6.0.12

## Start up, may take a long time to download

```
vagrant up
vagrant ssh u1  # login k8s master node
kubectl get pods --all-namespaces
```

## Optional
if in China, and have never installed a vagrant box, you can install vagrant box from a China mirror to speed up download:

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
