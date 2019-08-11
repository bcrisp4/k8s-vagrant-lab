# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'erb'

BOX = "fedora/30-cloud-base"
RAM = 1024
CPU = 2

masters = { 
    'qty' => 1, 
    'box' => BOX, 
    'pre' => 'master', 
    'ram' => RAM, 
    'cpu' => CPU,
    'playbook' => 'master.yml'
}

nodes = { 
    'qty' => 2, 
    'box' => BOX, 
    'pre' => 'node', 
    'ram' => RAM, 
    'cpu' => CPU,
    'playbook' => 'node.yml'
}

Vagrant.configure("2") do |config|

  config.vm.network :public_network,
    :dev => "br0",
    :mode => "bridge",
    :type => "bridge"

  config.vm.provision "shell", inline: "echo 'Screw with DNS settings' && sudo nmcli conn modify 'System eth1' ipv4.ignore-auto-dns yes && sudo systemctl restart NetworkManager"

  # define nodes
  start_port = 9002
  defined_nodes = []
  if nodes 
    nodes['qty'].times do |i|
      name = "#{nodes['pre']}-#{i}"
      defined_nodes.push(name)
      config.vm.define name do |node|
        node.vm.box       =  BOX
        node.vm.hostname  =  name
        node.vm.network "forwarded_port", guest: 30707, host: start_port + i
        node.vm.provider :libvirt do |domain|
          domain.cpus      =  nodes['cpu']
          domain.memory    =  nodes['ram']
        end
      end
    end
  end
  # end define nodes

  # define Masters
  master_port = 9001
  defined_masters = []
  if masters 
    masters['qty'].times do |i|
      name = "#{masters['pre']}-#{i}"
      defined_masters.push(name)
      config.vm.define name do |master|
        master.vm.box       =  BOX
        master.vm.hostname  =  name
        master.vm.network "forwarded_port", guest: 30707, host: master_port
        master.vm.provider :libvirt do |domain|
          domain.cpus      =  masters['cpu']
          domain.memory    =  masters['ram']
        end
        # Run ansible provisioner on all nodes/masters from the last generated master  
        if i == masters['qty'] - 1
          master.vm.synced_folder ".vagrant", "/vagrant/.vagrant"
          master.vm.provision "shell", inline: "rpm --quiet -q ansible && echo 'Ansible already installed' || echo 'Installing Ansible' && sudo dnf -q -y install ansible "
          master.vm.provision "shell", inline: "echo 'Installing Ansible roles' && cd /vagrant/ansible && ansible-galaxy install -r requirements.yml"
          master.vm.provision "ansible_local" do |ansible|
            ansible.verbose           = true
            ansible.provisioning_path = "/vagrant/ansible" 
            ansible.inventory_path    = "inventory"
            ansible.limit             = "all"
            ansible.playbook          = "site.yml"
          end
        end
      end
    end
  end
  # end define masters

  # create Ansible inventory file
  template = ERB.new(File.read("templates/inventory.erb"), nil, '-')
  out = template.result(binding)
  file = File.open( "ansible/inventory","w" )
  file << out
  file.close


end



