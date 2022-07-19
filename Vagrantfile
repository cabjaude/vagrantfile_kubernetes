Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/bionic64"
  
    config.vm.define "masterk8s" do |config|
    config.vm.network "public_network", bridge: "eno1" , :mac => "080027A26BFD"       
    config.vm.hostname = "master-k8s"
    config.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
      # v.gui = true
    end
  end
  
  config.vm.define "worker1k8s" do |config|
    config.vm.network "public_network", bridge: "eno1" , :mac => "080027A26BFD"       
    config.vm.hostname = "worker1k8s"
    config.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
      # v.gui = true
    end
  end
  
  config.vm.define "worker2k8s" do |config|
    config.vm.network "public_network", bridge: "eno1" , :mac => "080027A26BFD"       
    config.vm.hostname = "worker2k8s"
    config.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
      #v.gui = true
    end
  end
end




