# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

Vagrant.configure(2) do |config|


  ANSIBLE_RAW_SSH_ARGS = []
  VAGRANT_VM_PROVIDER = "virtualbox"
  machine_box = "boxcutter/ubuntu1604"

  config.vm.define "node1" do |machine|
    machine.vm.box = machine_box
    machine.vm.hostname = "node1"
    machine.vm.network "private_network", ip: "192.168.7.151"
    machine.vm.provider "virtualbox" do |node|
        node.name = "node1"
        node.memory = 1024
        node.cpus = 2
    end
   end


   config.vm.define "node2" do |machine|
     machine.vm.box = machine_box
     machine.vm.hostname = "node2"
     machine.vm.network "private_network", ip: "192.168.7.152"
     machine.vm.provider "virtualbox" do |node|
         node.name = "node2"
         node.memory = 1024
         node.cpus = 2
     end
    end


    config.vm.define "node3" do |machine|
      machine.vm.box = machine_box
      machine.vm.hostname = "node3"
      machine.vm.network "private_network", ip: "192.168.7.153"
      machine.vm.provider "virtualbox" do |node|
          node.name = "node3"
          node.memory = 1024
          node.cpus = 2
      end
     end




end
