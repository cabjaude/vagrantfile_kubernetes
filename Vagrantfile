Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"

  config.vm.define "masterk8s" do |masterk8s|
      masterk8s.vm.network "public_network", bridge: "enp3s0"        
      masterk8s.vm.hostname = "master-k8s"
      masterk8s.vm.provider "masterk8s" do |vb|
        vb.cpus = 2
        vb.memory = 2048
      end
    end
  
    config.vm.define "worker1k8s" do |worker1k8s|
      worker1k8s.vm.network "public_network", bridge: "enp3s0"
      worker1k8s.vm.hostname = "worker1-k8s"
      worker1k8s.vm.provider "worker1k8s" do |vb|
        vb.cpus = 2
        vb.memory = 2048
      end
    end

    config.vm.define "worker2k8s" do |worker2k8s|
      worker2k8s.vm.network "public_network", bridge: "enp3s0"
      worker2k8s.vm.hostname = "worker2-k8s"
      worker2k8s.vm.provider "worker2k8s" do |vb|
        vb.cpus = 2
        vb.memory = 2048
      end
    end
end