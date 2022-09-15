
# -*- mode: ruby -*-
# vi: set ft=ruby :

MASTER_IP       = "192.168.1.10"
NODE_01_IP      = "192.168.1.11"
NODE_02_IP      = "192.168.1.12"

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.box_version = "20220804.0.0"

  boxes = [
    { :name => "master",  :ip => MASTER_IP,  :cpus => 1, :memory => 2048 },
    { :name => "node-01", :ip => NODE_01_IP, :cpus => 2, :memory => 2048 },
    { :name => "node-02", :ip => NODE_02_IP, :cpus => 2, :memory => 2048 },
  ]

  boxes.each do |opts|
    config.vm.define opts[:name] do |box|
      box.vm.hostname = opts[:name]
      box.vm.network "public_network", bridge: "enp3s0", ip: opts[:ip]
 
      box.vm.provider "virtualbox" do |vb|
        vb.cpus = opts[:cpus]
        vb.memory = opts[:memory]
      end
      box.vm.provision "shell", path:"./install-kubernetes-dependencies.sh"
      if box.vm.hostname == "master" then 
        box.vm.provision "shell", path:"./configure-master-node.sh"
        end
      if box.vm.hostname == "node-01" then ##TODO: create some regex to match worker hostnames
        box.vm.provision "shell", path:"./configure-worker-nodes.sh"
      end
      if box.vm.hostname == "node-02" then ##TODO: create some regex to match worker hostnames
        box.vm.provision "shell", path:"./configure-worker-nodes.sh"
      end
      
    end
  end
end