# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'

$env = YAML::load_file('vagrant.yml')
store_dir = "#{ENV['HOME']}/.dir-vagrant"
storage_disk_size = 10480

Vagrant.configure('2') do |config|
    # Create and provision each host as defined in the site's YAML file
    $env['hosts'].each do |host_name, host_config|
        config.vm.define host_name do |host|
            #host.vm.box = 'opscode-centos-6.5'
            #host.vm.box_url = 'http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-6.5_chef-provisionerless.box'
            host.vm.box = $env['box_name']
            host.vm.box_url = $env['box_url']

            host.vm.synced_folder '.', '/vagrant', :disabled => false

            host.vm.network 'private_network', :ip => host_config['private_ip']
            host.vm.host_name = "#{host_name}.local"

            if host_config['forwarded-ssh-port']
                host.vm.network 'forwarded_port', :guest => 22, :host => host_config['forwarded-ssh-port']
            end

            # VirtualBox config
            host.vm.provider :virtualbox do |vbox|
                if host_config['memory']
                    vbox.customize ['modifyvm', :id, '--memory', host_config['memory']]
                end
                vbox.customize ['modifyvm', :id, '--usb', 'off']
                if host_config['role'] == 'storage'
                   vbox.customize ["storagectl", :id, "--name", "SATA", "--add", "sata",  "--controller", "IntelAHCI", "--portcount", "4", "--hostiocache", "off"]
                   for i in 1..3
                     disk_file = "#{store_dir}/#{host_name}_#{i.to_s}_#{rand(1000).to_s}.vdi"
                     vbox.customize ['createhd', '--filename', disk_file, '--size', storage_disk_size]
                     vbox.customize ['storageattach', :id, '--storagectl', "SATA", '--port', i, '--device', 0, '--type', 'hdd', '--medium', disk_file]
                   end
                end
            end

            # Update the /etc/hosts file so that each VM knows about each other
            host.vm.provision :shell, :inline => "sudo grep -q \"# Vagrant VMs\" /etc/hosts || echo -e \"\\n# Vagrant VMs\\n\" >> /etc/hosts"
            $env['hosts'].each do |inner_host_name, inner_host_config|
                if host_name != inner_host_name
                    host.vm.provision :shell, :inline => "sudo grep -q \" #{inner_host_name} \" /etc/hosts || echo \"#{inner_host_config['private_ip']} #{inner_host_name}.local #{inner_host_name}\" >> /etc/hosts"
                end
            end
        end
    end
end
