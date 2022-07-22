# IaC - Infrastructure as Code

	🚧  Deploying a k8s Cluster in Virtual Box 🚀 In Progress 🚧

<p align="center">
 <a href="#-sobre-o-projeto">Sobre</a> •
 <a href="#-funcionalidades">Funcionalidades</a> •
 <a href="#-pr%C3%A9-requisitos">Pré-requisitos</a> •
 <a href="#-instala%C3%A7%C3%A3o">Instalação</a> •
 <a href="#%EF%B8%8F-configura%C3%A7%C3%A3o">Configuração</a> •
 <a href="#-deploy">Deploy</a> •
 <a href="#-autor">Autor</a>
</p>

## 💻 About this project

Provision a Kubernetes Cluster with Vagrant in VirtualBox for Study Lab.

![Tux, the Linux mascot](/assets/images/tux.png)

## 💪 Features

- [x] Create Master Node
- [x] Create Workers Node
- [ ] Configured as instances via script in user-data
    - [x] Change Hostname
    - [ ] Deploying Zabbix Server with Docker Compose
    - [ ] Deploying Grafana Server with Docker Compose
    - [ ] Automate Install Script
---

### 🛠 Before you begin

Before starting, you will need to have the following tools installed on your machine:

[Git](https://git-scm.com) e [VSCode](https://code.visualstudio.com/).

<img height="32" width="32" src="https://unpkg.com/simple-icons@v7/icons/archlinux.svg.svg" /> Knowledge in Linux Environments<p>

Knowledge in Linux Vagrant<p>

Knowledge in Linux Kubernetes

---

### 🎲 Installation

#### Master k8s VM Configure
<ol>
<li> Update apt package list and upgrade.</li><p>

```console
apt-get update && apt-get upgrade -y
```

<li> Load the config modules of daemon k8s.</li><p>
 
 ```console
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
```

<li>Reload config modules</li><p>

 ```console
sysctl --system
```    
<li> Update the apt package index and install packages to allow apt to use a repository over HTTPS: </li><p>

 ```console
sudo apt-get update
sudo apt-get install -y \
   ca-certificates \
   curl \
   gnupg \
   lsb-release
```          

        
<li>Add Docker’s official GPG key (Necessary user root)</li><p>

```console
 sudo mkdir -p /etc/apt/keyrings
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  
``` 
<li>Use the following command to set up the repository:</li><p>


    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

<li>Install Docker Engine</li><p>
</li><p>
</ul>
    Update the apt package index, and install the latest version of Docker Engine, containerd, and Docker Compose, or go to the next step to install a specific version:
    
```console
      sudo apt-get update
      sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
``` 

Optional: Receiving a GPG error when running apt-get update?

  Your default umask may not be set correctly, causing the public key file for the repo to not be detected. Run the following command and then try to update your repo again: 
  
```console   
sudo chmod a+r /etc/apt/keyrings/docker.gpg.
``` 

<li>Configure Docker daemon in systemd</li><p>

```console  
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
```
 <li>Enable docker init statup with OS</li><p>

```console 
systemctl enable docker</li>
```
 <li>Reload daemon</li><p>

```console 
systemctl daemon-reload
```
 <li>Restart docker</li><p>

```console 
systemctl restart docker
```
 <li>Desabilitar o Swap</li><p>
 
```console 
swapoff -a
vim /etc/fstab (comentar a linha do swap)
```
</ol>
</ol>

# Installing kubeadm, kubelet and kubectl 

## You will install these packages on all of your machines: 

    kubeadm: the command to bootstrap the cluster.

    kubelet: the component that runs on all of the machines in your cluster and does things like starting pods and containers.

    kubectl: the command line util to talk to your cluster.

<ol><li>Update the apt package index and install packages needed to use the Kubernetes apt repository:</li><p>

```console 
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```
<li>Download the Google Cloud public signing key:</li><p>

```console 
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```

<li>Add the Kubernetes apt repository:</li><p>

```console
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
<li>Update apt package index, install kubelet, kubeadm and kubectl, and pin their version:</li><p>

```console
sudo apt-get update
sudo apt-get update && apt-get install kubeadm=1.21.0-00 kubelet=1.21.0-00 kubectl=1.21.0-00 -y
```

<li>Check if you installed it correctly with these commands:</li><p>

```console
kubeadm version
kubelet --version
kubectl version
```
<li>Download images container cluster.</li><p>

```console
kubeadm config images pull --kubernetes-version=1.21.0
```
<li>Init Cluster</li><p>

```console
kubeadm init --kubernetes-version=1.21.0
```

If everything went well you will receive the message below

Your Kubernetes control-plane has initialized successfully! 

<li>To start using your cluster, you need to run the following as a regular user:</li><p>

```console
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

<li>Enable CNI With plugin Weave Net</li><p>

```console
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

</ol>

#### Workers k8s VM Configure


### Set up the repository
<ol>
<li>Update the apt package index and install packages to allow apt to use a repository over HTTPS:</li>

```console
sudo apt-get update
sudo apt-get install -y \
   ca-certificates \
   curl \
   gnupg \
   lsb-release
```

<li>Add Docker’s official GPG key:</li><p>

```console
 sudo mkdir -p /etc/apt/keyrings
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  (Necessary user root)
```

<li>Use the following command to set up the repository:</li>

```console
     echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

<li>Install Docker Engine</li><p>

  Update the apt package index, and install the latest version of Docker Engine, containerd, and Docker Compose, or go to the next step to install a specific version:

```console
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
  Optional: Receiving a GPG error when running apt-get update?

  Your default umask may not be set correctly, causing the public key file for the repo to not be detected. Run the following command and then try to update your repo again: 
    
```console
sudo chmod a+r /etc/apt/keyrings/docker.gpg.
```

<li>Configure the Docker daemon in systemd</li><p>

```console
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
```

 
<li>Enable docker init statup with OS</li><p>

```console
systemctl enable docker
```
 <li>Reload daemon</li><p>

```console
systemctl daemon-reload
```
 <li>Restart docker</li><p>

```console
systemctl restart docker
```
<li>Installing kubeadm and kubelet</li>

  Update the apt package index and install packages needed to use the Kubernetes apt repository:

```console
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```
  Download the Google Cloud public signing key:

```console
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```
  Add the Kubernetes apt repository:

```console
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
  Update apt package index, install kubelet, kubeadm and kubectl, and pin their version:

```console
sudo apt-get update
sudo apt-get install -y kubeadm=1.21.1-00 kubelet=1.21.1-00
sudo kubeadm version && kubelet --version
``` 
<li>Disable swap</li><p>

```console
swapoff -a
vim /etc/fstab 
(comentar a linha do swap)
```
<li>Rename instance</li>

```console
vim /etc/hosts
```
</ol>
























