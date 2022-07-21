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

Provisionar um Cluster Kubernetes com Vagrant em VirtualBox para Laboratório de Estudos. ![Tux, the Linux mascot](/assets/images/tux.png)

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
<ol>
<li>1. Update apt package list and upgrade.</li>

        apt-get update && apt-get upgrade -y

<li>2. Load the config modules of daemon k8s.</li>
  
        cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
        br_netfilter
        EOF
     
        cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
        net.bridge.bridge-nf-call-ip6tables = 1
        net.bridge.bridge-nf-call-iptables = 1
        EOF

<ol><li> 2.1. Reload config modules</li></ol>  
  
        sysctl --system

<li>3. Update the apt package index and install packages to allow apt to use a repository over HTTPS:</li>

        sudo apt-get update

        sudo apt-get install -y \
           ca-certificates \
           curl \
           gnupg \
           lsb-release

<li>4. Add Docker’s official GPG key:</li>

 sudo mkdir -p /etc/apt/keyrings

 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  (Necessary user root)

<li>5. Use the following command to set up the repository:</li>

     echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

<li>6. Install Docker Engine</li>

    Update the apt package index, and install the latest version of Docker Engine, containerd, and Docker Compose, or go to the next step to install a specific version:

      sudo apt-get update

      sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

    Optional: Receiving a GPG error when running apt-get update?

      Your default umask may not be set correctly, causing the public key file for the repo to not be detected. Run the following command and then try to update your repo again: 
    
      sudo chmod a+r /etc/apt/keyrings/docker.gpg.


<li>7. Configure Docker daemon in systemd</li>

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

 <ol><li>7.1. Enable docker init statup with OS</li>

   systemctl enable docker<li>

 <li>7.2. Reload daemon<li>

  systemctl daemon-reload<li>

 <li>7.3. Restart docker<li>

  systemctl restart docker<li>

 <li>7.4. Desabilitar o Swap<li>
</ol>
  swapoff -a
  vim /etc/fstab (comentar a linha do swap)


# Installing kubeadm, kubelet and kubectl 

**You will install these packages on all of your machines:**

    kubeadm: the command to bootstrap the cluster.

    kubelet: the component that runs on all of the machines in your cluster and does things like starting pods and containers.

    kubectl: the command line util to talk to your cluster.

<ol><li>1. Update the apt package index and install packages needed to use the Kubernetes apt repository:</li>

sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl

<li>2. Download the Google Cloud public signing key:</li>

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

<li>3. Add the Kubernetes apt repository:</li>

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

<li>4. Update apt package index, install kubelet, kubeadm and kubectl, and pin their version:</li>

sudo apt-get update
sudo apt-get update && apt-get install kubeadm=1.21.0-00 kubelet=1.21.0-00 kubectl=1.21.0-00 -y

<li>5. Check if you installed it correctly with these commands:</li>

kubeadm version
kubelet --version
kubectl version

<li>6. Download images container cluster.</li>

kubeadm config images pull --kubernetes-version=1.21.0

<li>7. Init Cluster</li>

kubeadm init --kubernetes-version=1.21.0

If everything went well you will receive the message below

Your Kubernetes control-plane has initialized successfully! 

<li>8. To start using your cluster, you need to run the following as a regular user:</li>

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

<li>9. Enable CNI With plugin Weave Net</li>

kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"

</ol>

#### Workers k8s VM Configure


Set up the repository

<li>1. Update the apt package index and install packages to allow apt to use a repository over HTTPS:</li>

 sudo apt-get update

 sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

<li>2. Add Docker’s official GPG key:</li>

 sudo mkdir -p /etc/apt/keyrings

 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  (Necessary user root)

<li>3. Use the following command to set up the repository:</li>

     echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

<li>4. Install Docker Engine</li>

    Update the apt package index, and install the latest version of Docker Engine, containerd, and Docker Compose, or go to the next step to install a specific version:

      sudo apt-get update

      sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

    Optional: Receiving a GPG error when running apt-get update?

      Your default umask may not be set correctly, causing the public key file for the repo to not be detected. Run the following command and then try to update your repo again: 
    
      sudo chmod a+r /etc/apt/keyrings/docker.gpg.

<li>5. Configure the Docker daemon in systemd</li>

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

 <ol><li>5.1. Enable docker init statup with OS</li></ol>
   systemctl enable docker

 <li>5.2. Reload daemon</li>

   systemctl daemon-reload

 <li>5.3. Restart docker</li>

   systemctl restart docker
 
<li>6. Installing kubeadm and kubelet</li>

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
 
<li>7. Disable swap</li>

    swapoff -a
    vim /etc/fstab 
    (comentar a linha do swap)

<li>8. Rename instance</li>

    vim /etc/hosts





























apt-get install -y apt-transport-https ca-certificates curl

curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

apt-get update && apt-get install kubeadm=1.21.0-00 kubelet=1.21.0-00 -y
 
