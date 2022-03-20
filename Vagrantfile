# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

    config.vm.box = "ubuntu/focal64"
    config.vm.box_check_update = true

    config.vm.synced_folder "./configs", "/opt/configs"
    config.vm.synced_folder "./playbooks", "/opt/playbooks"

    config.vm.provider "virtualbox" do |vb|
        # Display the VirtualBox GUI when booting the machine
        vb.gui = false
    end

    config.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get upgrade -y
        echo "10.0.10.100    master01" >> /etc/hosts
        echo "10.0.10.101    worker01" >> /etc/hosts
        echo "10.0.10.102    worker02" >> /etc/hosts
        apt-get install -y ansible
        ansible-galaxy collection install community.general
        cd /home/vagrant/
        cp /opt/configs/ansible.cfg /home/vagrant/
        ansible-playbook -b /opt/playbooks/01-kubernetes_prep.yml
        ansible-playbook -b /opt/playbooks/02-kubernetes_install.yml
    SHELL

    config.vm.define "master01" do |master01|
        master01.vm.hostname = "master01"
        master01.vm.network "private_network", ip: "10.0.10.100"
        master01.vm.provider "virtualbox" do |vb|
            vb.memory = 4096
            vb.cpus = 4
        end
        master01.vm.provision "shell", inline: <<-SHELL
            cd /home/vagrant/
            ansible-playbook -b /opt/playbooks/03-cluster_master.yml
        SHELL
    end

    (1..2).each do |i|
        config.vm.define "worker0#{i}" do |node|
            node.vm.hostname = "worker0#{i}"
            node.vm.network "private_network", ip: "10.0.10.#{i+100}"
            node.vm.provider "virtualbox" do |vb|
                vb.memory = 2048
                vb.cpus = 2
            end
            node.vm.provision "shell", inline: <<-SHELL
                cd /home/vagrant
                ansible-playbook -b /opt/playbooks/04-cluster_worker.yml
            SHELL
        end
    end

end
