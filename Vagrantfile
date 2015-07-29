# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  ## Use default insecure key
  config.ssh.insert_key = false

  ## Disable shared folders
  config.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true

  config.vm.box = "chef/centos-7.0"
  config.vm.box_url = "chef/centos-7.0"

  config.vm.provider :vmware_fusion do |v|
    v.vmx["memsize"]  = 2048
    v.vmx["numvcpus"] = 2
  end

  config.vm.provider :virtualbox do |v|
    v.customize ["modifyvm", :id, "--memory", 2048]
    v.customize ["modifyvm", :id, "--cpus", 2]
  end

  # masters
  (1..3).each do |n|
    config.vm.define "mm#{n}" do |config|
      config.vm.hostname = "mm#{n}"
      config.vm.network :private_network, ip: "10.12.12.1#{n}"
    end
    config.vm.provision :ansible do |ansible| 
      ansible.playbook = "provisioning/site.yml"
      ansible.inventory_path = "provisioning/hosts"
      ansible.sudo = true
    end
  end

  # slaves
  (1..3).each do |n|
    config.vm.define "ms#{n}" do |config|
      config.vm.hostname = "ms#{n}"
      config.vm.network :private_network, ip: "10.12.12.2#{n}"
    end
    config.vm.provision :ansible do |ansible|
      ansible.playbook = "provisioning/site.yml"
      ansible.inventory_path = "provisioning/hosts"
      ansible.sudo = true
    end
  end

end
