
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  os = "ubuntu/xenial64"
  net_ip = "192.168.50"
  num_minions = 2
  vm_memory = "1024"

  config.vm.define "master", primary: true do |master_config|
    master_config.vm.provider "virtualbox" do |vb|
        vb.memory = "#{vm_memory}"
        vb.cpus = 1
        vb.name = "salt-master"
    end
    
    master_config.vm.box = "#{os}"
    master_config.vm.host_name = 'saltmaster.local'
    master_config.vm.network "private_network", ip: "#{net_ip}.10"

    master_config.vm.synced_folder "salt/", "/srv/salt"
    master_config.vm.synced_folder "pillar/", "/srv/pillar"

    master_config.vm.provision :salt do |salt|
      salt.install_master = true
      salt.no_minion = true
      salt.colorize = true
      salt.master_config = "etc/master"
    end
  end


  (1..num_minions).each do |i|
    vm_name = "minion#{i}"
    ip = "#{net_ip}.#{20+i}"

    config.vm.define "#{vm_name}" do |minion_config|
      minion_config.vm.provider "virtualbox" do |vb|
          vb.memory = "#{vm_memory}"
          vb.cpus = 1
          vb.name = "#{vm_name}"
      end

      minion_config.vm.box = "#{os}"
      minion_config.vm.hostname = "#{vm_name}.local"
      minion_config.vm.network "private_network", ip: "#{ip}"

      config.vm.provision "shell", 
        inline: "echo '#{net_ip}.10 saltmaster.local' >> /etc/hosts"

      minion_config.vm.provision :salt do |salt|
        salt.install_master = false
        salt.no_minion = false
        salt.minion_config = "etc/minion"
      end
    end
  end
end
