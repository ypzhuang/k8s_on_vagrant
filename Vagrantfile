# -*- mode: ruby -*-
# vi: set ft=ruby :

NODE_VCPUS = 2 #number of cpu
NODE_MASTER_MEMORY_SIZE = 2048 # memory size of master node(MB)
NODE_WORKER_MEMORY_SIZE = 2048 # memory size of worker nodes(MB)
NODES = 3 # number of nodes, you can set 1 only for a master node
IN_CHINA = 'yes' # yes or no,  yes: who who cannot connect to Google K8S source.
UBUNTU_RELEASE = "ubuntu/bionic64"  # ubuntu/xenial64:16.04 ubuntu/bionic64:18.04

def workerIP(num)
  return "192.168.9.1#{num}"
end

Vagrant.configure("2") do |config|
   # always use Vagrant's insecure key
  config.ssh.insert_key = false

  config.vm.box = UBUNTU_RELEASE 
  config.vm.box_check_update = false

  config.vm.provider "virtualbox" do |vb|   
    vb.cpus = NODE_VCPUS
    vb.gui = false     
  end

  (1..NODES).each do |i|
    config.vm.define vm_name = "u%d" % i do |worker|
      worker.vm.hostname = vm_name

      worker.vm.provider :virtualbox do |vb|
        if i === 1
          vb.memory = NODE_MASTER_MEMORY_SIZE
        else
          vb.memory = NODE_WORKER_MEMORY_SIZE
        end
      end
      worker.vm.network :private_network, ip: workerIP(i)
    end
  end

  config.vm.provision "shell", privileged: false, args: [IN_CHINA], inline: <<-SHELL 
    if [[ $1 == "yes" ]]; then
        echo 'Set Time Zone'
        echo "export TZ=Asia/Shanghai" | tee -a ~/.bashrc       
    fi    
  SHELL

  config.vm.provision "shell", privileged: false, args: [IN_CHINA], inline: <<-SHELL
    if [[ $1 == "yes" ]]; then
      echo "Using Ali Mirror"       
      echo """
deb http://mirrors.aliyun.com/ubuntu/ $(lsb_release -cs) main restricted
deb http://mirrors.aliyun.com/ubuntu/ $(lsb_release -cs)-updates main restricted
deb http://mirrors.aliyun.com/ubuntu/ $(lsb_release -cs) universe
deb http://mirrors.aliyun.com/ubuntu/ $(lsb_release -cs)-updates universe
deb http://mirrors.aliyun.com/ubuntu/ $(lsb_release -cs) multiverse
deb http://mirrors.aliyun.com/ubuntu/ $(lsb_release -cs)-updates multiverse
deb http://mirrors.aliyun.com/ubuntu/ $(lsb_release -cs)-backports main restricted universe multiverse
""" | sudo  tee /etc/apt/sources.list
    fi
  SHELL

  config.vm.provision "shell", privileged: false, args: [IN_CHINA], inline: <<-SHELL 
    echo "Install docker......"    

    if (( $(ps -ef | grep -v grep | grep docker | wc -l) > 0 ))
    then
      echo "docker is running!!!"
    else 
       sudo apt-get update
       sudo apt-get install -y \
        apt-transport-https \
        ca-certificates \
        curl \
        software-properties-common 
      
      if [[ $1 == "yes" ]]; then
        # Ali docker install source for China users to speed up to download
        echo 'Add Ali docker install source...'
        curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
        sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
      else
        # offical docker install source
        echo 'Add offical docker install source...'
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
        sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
      fi 
      
      docker_version=5:18.09.9~3-0~ubuntu-$(lsb_release -cs)
      sudo apt-get update && sudo apt-get install -y docker-ce=${docker_version} docker-ce-cli=${docker_version} containerd.io      

      if [ ! -f  /etc/docker/daemon.json ]; then
        echo '''
          {
            "exec-opts": ["native.cgroupdriver=systemd"],
            "log-driver": "json-file",
            "log-opts": {
              "max-size": "100m",
              "max-file": "3"
            },
            "storage-driver": "overlay2"
          }
        ''' | sudo tee  /etc/docker/daemon.json
      fi


      sudo usermod -aG docker $USER  
      newgrp docker

      sudo systemctl daemon-reload
      sudo systemctl restart docker 
      # sudo systemctl enable docker
      # sudo /lib/systemd/systemd-sysv-install enable docker
    fi
  SHELL


  config.vm.provision "shell", privileged: false, args: [NODES], inline: <<-SHELL
    if grep -qF "192.168.9" /etc/hosts;then
      echo "Host mapping skips."
    else
       echo "Host name mapping"       
       number=$1
       for i in $(seq 1 $number) 
       do  
         echo "192.168.9.1$i u$i" | sudo tee -a /etc/hosts
       done       
    fi     
  SHELL

  # config.vm.provision "shell", privileged: false, inline: <<-SHELL 
  #     echo 'Install docker-compose'
  #     if [ ! -f  /usr/local/bin/docker-compose ]; then
  #       sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  #       sudo chmod +x /usr/local/bin/docker-compose
  #     fi
  # SHELL



  # config.vm.provision "shell", privileged: false, inline: <<-SHELL 
  #     echo 'Install Go'
  #     VERSION=1.13.1
  #     OS=linux
  #     ARCH=amd64
  #     curl -o go$VERSION.$OS-$ARCH.tar.gz  https://dl.google.com/go/go$VERSION.$OS-$ARCH.tar.gz      
  #     sudo tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz
  #     echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.profile

  # SHELL

  # config.vm.provision "shell", privileged: false, inline: <<-SHELL 
  #     echo 'Install utils'
  #     sudo apt-get update
  #     sudo apt-get install -y unzip zip      
  # SHELL


  config.vm.provision "shell", privileged: false, args: [IN_CHINA], inline: <<-SHELL 
      echo 'Install k8s'
      k8s_version=1.16.2-00 #1.16.0-00 1.16.1-00
      sudo apt-get update && sudo apt-get install -y apt-transport-https curl   
     
      if [[ $1 == "yes" ]]; then
        # Ali k8S install source for China users to speed up to download
        curl -s https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -
        echo "deb https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
      else
        # Offical k8S install source
        curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
        echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
      fi

      sudo apt-get update
      sudo apt-get install -y kubelet=$k8s_version kubeadm=$k8s_version kubectl=$k8s_version
      sudo apt-mark hold kubelet kubeadm kubectl
     
      if [[ $1 == "yes" ]]; then
         # load k8s images from files 
         if [[ ! -d /vagrant/images ]]; then
            tar zxvf /vagrant/images.tar.gz -C /vagrant/
         fi
         cd /vagrant/ && sudo ./dimage load
      else
         # if you can go through GFW to download k8s images from Google Container Server
         sudo kubeadm config images pull
      fi     
  SHELL

  config.vm.provision "shell", privileged: false, args: [IN_CHINA], inline: <<-SHELL 
    echo 'Config cluster of k8s'
    hostname=$(hostname)
    if [[ $hostname == "u1" ]]; then
       echo 'manager node...'
      
      sudo kubeadm init --apiserver-advertise-address=192.168.9.11  --pod-network-cidr=10.244.0.0/16 #Canal

      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      sudo chown $(id -u):$(id -g) $HOME/.kube/config

      kubectl taint nodes --all node-role.kubernetes.io/master-

      if [[ $1 == "yes" ]]; then
        kubectl apply -f /vagrant/k8s_config/canal.yaml 
      else
        kubectl apply -f https://docs.projectcalico.org/v3.8/manifests/canal.yaml
      fi

      echo 'source <(kubectl completion bash)' >>~/.bashrc
      echo 'alias k=kubectl' >>~/.bashrc
      echo 'complete -F __start_kubectl k' >>~/.bashrc
      #kubectl completion bash | sudo tee /etc/bash_completion.d/kubectl

      echo 'get join command...'
      token=$(kubeadm token list | grep -v TOKEN | awk '{printf("%s", $1)}')
      hash=$(openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //')
      echo "sudo kubeadm join 192.168.9.11:6443 --token $token --discovery-token-ca-cert-hash sha256:$hash" > /vagrant/worker_join.sh 
      chmod +x /vagrant/worker_join.sh 
    else
      echo 'worker node...'
      /vagrant/worker_join.sh 
    fi
  SHELL


end
