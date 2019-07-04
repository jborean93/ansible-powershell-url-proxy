# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'

inventory = YAML.load_file('inventory.yml')

host_count = inventory['all']['children']['windows']['hosts'].keys.count + inventory['all']['children']['linux']['hosts'].keys.count
host_idx = 1

Vagrant.configure("2") do |config|
  inventory['all']['children'].each do |group,details|
    details['hosts'].each do |server,host_details|
      config.vm.define server do |srv|
        srv.vm.box = host_details['vagrant_box']
        srv.vm.hostname = server
        srv.vm.network :private_network, ip: host_details['ansible_host']

        srv.vm.provider :virtualbox do |v|
          v.name = File.basename(File.dirname(__FILE__)) + "_" + server + "_" + Time.now.to_i.to_s
          v.gui = false
        end

        if group == 'linux' then
         config.vm.provision 'shell', inline: "sed -re 's/^(PasswordAuthentication)([[:space:]]+)no/\\1\\2yes/' -i /etc/ssh/sshd_config; systemctl restart sshd"
        end

        if host_idx == host_count then
          config.vm.provision 'ansible' do |ansible|
            ansible.playbook = 'main.yml'
            ansible.limit = 'all'
            ansible.inventory_path = 'inventory.yml'
            ansible.verbose = '-vv'
          end
        end

        host_idx = host_idx + 1
      end
    end
  end
end
