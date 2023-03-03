NUM_WORKER_NODES=2 #quantidade de workers
IP_NW="192.168.0." #rede local a ser utilizada.
IP_START=2 #octeto inicio o qual dara a sequecia do bloco de rede que será implementado no Master o primeiro da sequencia.

Vagrant.configure("2") do |config|
  config.vm.provision "shell", env: {"IP_NW" => IP_NW, "IP_START" => IP_START}, inline: <<-SHELL
      apt-get update -y
      echo "$IP_NW$((IP_START)) master-node" >> /etc/hosts
      echo "$IP_NW$((IP_START+1)) worker-node01" >> /etc/hosts
      echo "$IP_NW$((IP_START+2)) worker-node02" >> /etc/hosts
  SHELL

  config.vm.box = "ubuntu/focal64"
  config.vm.box_check_update = true

  config.vm.define "master" do |master|   
    master.vm.hostname = "master-node"
    master.vm.network "public_network", bridge: "wlp4s0" , ip: IP_NW + "#{IP_START}" #wlp4s0 interface da rede
    master.vm.provider "virtualbox" do |vb|
        vb.memory = 2048
        vb.cpus = 2
    end
    master.vm.provision "shell", path: "scripts/common.sh"
    master.vm.provision "shell", path: "scripts/master.sh"
  end

  (1..NUM_WORKER_NODES).each do |i|

  config.vm.define "node0#{i}" do |node|
    node.vm.hostname = "worker-node0#{i}"
    node.vm.network "public_network",  bridge: "wlp4s0" , ip: IP_NW + "#{IP_START + i}" #wlp4s0 interface da rede
    node.vm.provider "virtualbox" do |vb|
        vb.memory = 2048
        vb.cpus = 2
    end
    node.vm.provision "shell", path: "scripts/common.sh"
    node.vm.provision "shell", path: "scripts/node.sh"
  end

  end
end 