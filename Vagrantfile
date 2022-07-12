Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"

  config.vm.define "masterk8s" do |masterk8s|
    #Trocar o eno1 pela sua interface local
      masterk8s.vm.network "public_network", bridge: "eno1" , :mac => "080027A26BFD"       
      masterk8s.vm.hostname = "master-k8s"
      masterk8s.vm.provider "masterk8s" do |v|
        v.cpus = 2
        v.memory = 2048
      end
    end
  
    config.vm.define "worker1k8s" do |worker1k8s|
    #Trocar o eno1 pela sua interface local
      worker1k8s.vm.network "public_network", bridge: "eno1" , :mac => "0800278E6BB3"
      worker1k8s.vm.hostname = "worker1-k8s"
      worker1k8s.vm.provider "worker1k8s" do |v|  
        v.cpus = 2
        v.memory = 2048
      end
    end

    config.vm.define "worker2k8s" do |worker2k8s|
    #Trocar o eno1 pela sua interface local 
      worker2k8s.vm.network "public_network", bridge: "eno1" , :mac => "0800270DBE45"
      worker2k8s.vm.hostname = "worker2-k8s"
      worker2k8s.vm.provider "worker2k8s" do |v|
        v.cpus = 2
        v.memory = 2048
      end
    end
end