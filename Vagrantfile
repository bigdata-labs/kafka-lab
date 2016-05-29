# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.



  ANSIBLE_RAW_SSH_ARGS = []
  VAGRANT_VM_PROVIDER = "virtualbox"
  machine_box = "trusty-server-cloudimg-amd64-vagrant-disk1"
  machine_box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"

  zookeeper_node_count=3
  (1..zookeeper_node_count).each do |machine_id|
    ANSIBLE_RAW_SSH_ARGS << "-o IdentityFile=./.vagrant/machines/zookeeper#{machine_id}/#{VAGRANT_VM_PROVIDER}/private_key"
  end


  (1..zookeeper_node_count).each do |machine_id|
    config.vm.define "zookeeper#{machine_id}" do |machine|
      machine.vm.box = machine_box
      machine.vm.box_url = machine_box_url
      machine.vm.hostname = "zookeeper#{machine_id}"
      machine.vm.network "private_network", ip: "192.168.2.#{150+machine_id}"
      # machine.vm.synced_folder "provision/files/ssh/authorized_keys", "/home/spark/.ssh/authorized_keys", create:true
      machine.vm.provider "virtualbox" do |node|
          node.name = "zookeeper#{machine_id}"
          node.memory = 256
          node.cpus = 1
      end
      if machine_id == zookeeper_node_count
          machine.vm.provision :ansible do |ansible|
                  ansible.playbook = "provision/zookeeper.yml"
                  ansible.inventory_path = 'provision/inventory'
                  ansible.verbose = "vvvv"
                  ansible.raw_ssh_args = ANSIBLE_RAW_SSH_ARGS
                  ansible.limit = 'zookeeper'
          end
      end
     end
  end




  ##kafka1 node
  config.vm.define "kafka1" do |node|
      node.vm.box = machine_box
      node.vm.box_url = machine_box_url
      node.vm.hostname = "kafka1"
      node.vm.network "private_network", ip: "192.168.2.161"
      node.vm.network "forwarded_port", guest: 9092, host: 19092


      node.vm.provider "virtualbox" do |n|
          n.name = "kafka1"
          n.memory = 512
          n.cpus = 1
      end

      node.vm.provision :ansible do |ansible|
          ansible.playbook = "provision/kafka1.yml"
          ansible.inventory_path = 'provision/inventory'
          ansible.raw_ssh_args = ANSIBLE_RAW_SSH_ARGS
          ansible.limit = 'kafka1'
      end
  end

  ##app
  config.vm.define "app" do |node|
      node.vm.box = machine_box
      node.vm.box_url = machine_box_url
      node.vm.hostname = "app"
      node.vm.network "private_network", ip: "192.168.2.12"
      node.vm.network "forwarded_port", guest: 8086, host: 18086

      node.vm.provider "virtualbox" do |n|
          n.name = "app"
          n.memory = 512
          n.cpus = 1
      end

      node.vm.provision :ansible do |ansible|
          ansible.playbook = "provision/app.yml"
          ansible.inventory_path = 'provision/inventory'
          ansible.raw_ssh_args = ANSIBLE_RAW_SSH_ARGS
          ansible.limit = 'app'
      end
  end

  ## #kafka2 node
  config.vm.define "kafka2" do |node|
      node.vm.box = machine_box
      node.vm.box_url = machine_box_url
      node.vm.hostname = "kafka2"
      node.vm.network "private_network", ip: "192.168.2.162"
      node.vm.network "forwarded_port", guest: 9092, host: 19093

      node.vm.provider "virtualbox" do |n|
          n.name = "kafka2"
          n.memory = 512
          n.cpus = 1
      end

      node.vm.provision :ansible do |ansible|
          ansible.playbook = "provision/kafka2.yml"
          ansible.inventory_path = 'provision/inventory'
          ansible.raw_ssh_args = ANSIBLE_RAW_SSH_ARGS
          ansible.limit = 'kafka2'
      end
  end


  ## log kanban node
  config.vm.define "log" do |node|
      node.vm.box = machine_box
      node.vm.box_url = machine_box_url
      node.vm.hostname = "log"
      node.vm.network "private_network", ip: "192.168.2.77"
      node.vm.network "forwarded_port", guest: 9200, host: 19200
      node.vm.network "forwarded_port", guest: 5601, host: 15601
      node.vm.network "forwarded_port", guest: 5000, host: 15000


      node.vm.synced_folder "logs/", "/var/logs/", create:true

      node.vm.provider "virtualbox" do |n|
          n.name = "log"
          n.memory = 1024
          n.cpus = 4
      end

      node.vm.provision :ansible do |ansible|
          ansible.playbook = "provision/log.yml"
          ansible.inventory_path = 'provision/inventory'
          ansible.raw_ssh_args = ANSIBLE_RAW_SSH_ARGS
          ansible.limit = 'log'
      end
  end

end
