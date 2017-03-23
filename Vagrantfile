# -*- mode: ruby -*-
# vi: set ft=ruby :

# Don't change this IP, the kubernetes setup contains tls certificates for this
# IP. If you change it, the node can't connect to the master.
$master_ip = "192.168.200.2"

Vagrant.configure(2) do |config|
  # TODO insert default insecure key  
  config.ssh.username = "root"
  config.ssh.password = "vagrant"
  config.vm.synced_folder ".", "/demo", :disabled => true
  config.nfs.functional = false

  config.vm.provider :libvirt do |domain|
    domain.cpus = 2
    domain.nested = true  # enable nested virtualization
    domain.cpu_mode = "host-model"
  end

  config.vm.define "master" do |master|
    master.vm.box = "rmohr/kubevirt-master"
    master.vm.hostname = "master"
    master.vm.network "private_network", ip: $master_ip
    master.vm.provider :libvirt do |domain|
      domain.memory = 3000
    end
  end

  config.vm.define "node0" do |node|
    node.vm.box = "rmohr/kubevirt-node0"
    node.vm.hostname = "node0"
    puts $master_ip[0..-2] + ($master_ip[-1].to_i + 1).to_s
    node.vm.network "private_network", ip: $master_ip[0..-2] + ($master_ip[-1].to_i + 1).to_s
    node.vm.provider :libvirt do |domain|
      domain.memory = 2048
    end
  end
end
