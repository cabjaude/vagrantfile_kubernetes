# IaC - Infraestructure as Code

	🚧  Deploy de um Cluster k8s em Virtual Box 🚀 Em andamento  🚧

<p align="center">
 <a href="#-sobre-o-projeto">Sobre</a> •
 <a href="#-funcionalidades">Funcionalidades</a> •
 <a href="#-pr%C3%A9-requisitos">Pré-requisitos</a> •
 <a href="#-instala%C3%A7%C3%A3o">Instalação</a> •
 <a href="#%EF%B8%8F-configura%C3%A7%C3%A3o">Configuração</a> •
 <a href="#-deploy">Deploy</a> •
 <a href="#-autor">Autor</a>
</p>

## 💻 Sobre o projeto

Provisionar um Cluster Kubernetes com Vagrant em VirtualBox para Laboratório de Estudos.

## 💪 Funcionalidades

- [x] Cria 1 nó Master
- [x] Cria 2 Workers
- [ ] Configura as instâncias através de script no user-data
    - [ ] Deploy de Zabbix Server
    - [ ] Deploy de Grafana Server 
    - [x] Troca de Hostname
---

### 🛠 Pré-requisitos

Antes de começar, você vai precisar ter instalado em sua máquina as seguintes ferramentas:
[Git](https://git-scm.com) e [VSCode](https://code.visualstudio.com/).

Conhecimentos em Linux
Conhecimentos em Vagrant
Conhecimentos básico de Kubernetes 

---

### 🎲 Instalação

#### Master k8s VM Configure

1. Update apt package list and upgrade.

        apt-get update && apt-get upgrade -y

2. Load the config modules of daemon k8s.
  
        cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
        br_netfilter
        EOF
     
        cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
        net.bridge.bridge-nf-call-ip6tables = 1
        net.bridge.bridge-nf-call-iptables = 1
        EOF

  2.1. Reload config modules  
  
        sysctl --system

3. Update the apt package index and install packages to allow apt to use a repository over HTTPS:

 sudo apt-get update

 sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

4. Add Docker’s official GPG key:

 sudo mkdir -p /etc/apt/keyrings

 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  (Necessary user root)

5. Use the following command to set up the repository:

     echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

6. Install Docker Engine

    Update the apt package index, and install the latest version of Docker Engine, containerd, and Docker Compose, or go to the next step to install a specific version:

      sudo apt-get update

      sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

    Optional: Receiving a GPG error when running apt-get update?

      Your default umask may not be set correctly, causing the public key file for the repo to not be detected. Run the following command and then try to update your repo again: 
    
      sudo chmod a+r /etc/apt/keyrings/docker.gpg.


7. Configure Docker daemon in systemd

  cat > /etc/docker/daemon.json <<EOF
  {
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
      "max-size": "100m"
    },
    "storage-driver": "overlay2"
  }
  EOF

 7.1. Enable docker init statup with OS

   systemctl enable docker

 7.2. Reload daemon

  systemctl daemon-reload

 7.3. Restart docker 

  systemctl restart docker

 7.4. Desabilitar o Swap*

  swapoff -a
  vim /etc/fstab (comentar a linha do swap)


# Installing kubeadm, kubelet and kubectl 

**You will install these packages on all of your machines:**

    kubeadm: the command to bootstrap the cluster.

    kubelet: the component that runs on all of the machines in your cluster and does things like starting pods and containers.

    kubectl: the command line util to talk to your cluster.

1. Update the apt package index and install packages needed to use the Kubernetes apt repository:

sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl

2. Download the Google Cloud public signing key:

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

3. Add the Kubernetes apt repository:

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

4. Update apt package index, install kubelet, kubeadm and kubectl, and pin their version:

sudo apt-get update
sudo apt-get update && apt-get install kubeadm=1.21.0-00 kubelet=1.21.0-00 kubectl=1.21.0-00 -y

5. Check if you installed it correctly with these commands:

kubeadm version
kubelet --version
kubectl version

6. Download images container cluster.

kubeadm config images pull --kubernetes-version=1.21.0

7. Init Cluster

kubeadm init --kubernetes-version=1.21.0

If everything went well you will receive the message below

Your Kubernetes control-plane has initialized successfully! 

8. To start using your cluster, you need to run the following as a regular user:

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

9. Enable CNI With plugin Weave Net

kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"


#### Workers k8s VM Configure


Set up the repository

1. Update the apt package index and install packages to allow apt to use a repository over HTTPS:

 sudo apt-get update

 sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

2. Add Docker’s official GPG key:

 sudo mkdir -p /etc/apt/keyrings

 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  (Necessary user root)

3. Use the following command to set up the repository:

     echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

4. Install Docker Engine

    Update the apt package index, and install the latest version of Docker Engine, containerd, and Docker Compose, or go to the next step to install a specific version:

      sudo apt-get update

      sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

    Optional: Receiving a GPG error when running apt-get update?

      Your default umask may not be set correctly, causing the public key file for the repo to not be detected. Run the following command and then try to update your repo again: 
    
      sudo chmod a+r /etc/apt/keyrings/docker.gpg.

5. Configure the Docker daemon in systemd

      cat > /etc/docker/daemon.json <<EOF
      {
        "exec-opts": ["native.cgroupdriver=systemd"],
        "log-driver": "json-file",
        "log-opts": {
          "max-size": "100m"
        },
        "storage-driver": "overlay2"
      }
      EOF

 5.1. Enable docker init statup with OS

   systemctl enable docker

 5.2. Reload daemon

   systemctl daemon-reload

 5.3. Restart docker 

   systemctl restart docker
 
6. Installing kubeadm and kubelet 

  Update the apt package index and install packages needed to use the Kubernetes apt repository:

    sudo apt-get update
    sudo apt-get install -y apt-transport-https ca-certificates curl

  Download the Google Cloud public signing key:

    sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

  Add the Kubernetes apt repository:

    echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

  Update apt package index, install kubelet, kubeadm and kubectl, and pin their version:

    sudo apt-get update
    sudo apt-get install -y kubeadm=1.21.1-00 kubelet=1.21.1-00
    sudo kubeadm version && kubelet --version
 
7. Disable swap

    swapoff -a
    vim /etc/fstab 
    (comentar a linha do swap)

8. Rename instance

    vim /etc/hosts





























apt-get install -y apt-transport-https ca-certificates curl

curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

apt-get update && apt-get install kubeadm=1.21.0-00 kubelet=1.21.0-00 -y
 
