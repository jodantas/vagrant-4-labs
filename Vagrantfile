# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'yaml'

yaml = YAML.load_file("machines.yml")

Vagrant.configure("2") do |config|
  yaml.each do |server|

    config.trigger.before :up do |before|
      before.ruby do |env,machine|
        File.open('.hosts.tmp', 'a') do |hosts|
          hosts.write("127.0.0.1 localhost \n#{server['ip']} #{server['hostname']} \n")
        File.open("hosts", "w+") { |file| file.puts File.readlines(".hosts.tmp").uniq }
        end
        if !(File.exists?('id_rsa'))
          system("ssh-keygen -b 2048 -t rsa -f id_rsa -q -N ''")
        end
      end
    end

    config.vm.define server["name"] do |srv|
      srv.vm.box = server["system"]
      srv.vm.network "private_network", ip: server["ip"]
      srv.vm.hostname = server["hostname"]
      srv.vm.provider "virtualbox" do |vb|
        vb.name = server["name"]
        vb.memory = server["memory"]
        vb.cpus = server["cpus"]
      end

      if server["system"] == "ubuntu/bionic64"
        srv.vm.provision "shell", inline: "apt install python -y"
      end

      config.vm.provision "shell", inline: "cp /vagrant/hosts /etc/hosts"
      config.vm.provision "shell", inline: "mkdir -p /root/.ssh"
      config.vm.provision "shell", inline: "cp /vagrant/id_rsa /root/.ssh/id_rsa"
      config.vm.provision "shell", inline: "cp /vagrant/id_rsa.pub /root/.ssh/authorized_keys"
      config.vm.provision "shell", inline: "chmod 600 /root/.ssh/id_rsa"
      
    end
  end
end
