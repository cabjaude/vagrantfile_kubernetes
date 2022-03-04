Vagrant.configure("2") do |config|
    config.vm.box = "bento/ubuntu-20.04"

    config.vm.define "cluster01" do |cluster01|
        cluster01.vm.network "public_network", bridge: "wlp4s0"        
        cluster01.vm.hostname = "cluster01"
        cluster01.vm.provider "cluster01" do |vb|
          vb.cpus = 1
          vb.memory = 1024
        end
      end
    
      config.vm.define "cluster02" do |cluster02|
        cluster02.vm.network "public_network", bridge: "wlp4s0"
        cluster02.vm.hostname = "cluster02"
        cluster02.vm.provider "cluster02" do |vb|
          vb.cpus = 1
          vb.memory = 1024
        end
      end

      config.vm.define "cluster03" do |cluster03|
        cluster03.vm.network "public_network", bridge: "wlp4s0"
        cluster03.vm.hostname = "cluster03"
        cluster03.vm.provider "cluster03" do |vb|
          vb.cpus = 1
          vb.memory = 1024
        end
      end
  end