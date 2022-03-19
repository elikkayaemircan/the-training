# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

    config.vm.box = "ubuntu/focal64"
    config.vm.box_check_update = true

    config.vm.provider "virtualbox" do |vb|
        # Display the VirtualBox GUI when booting the machine
        vb.gui = false
    end

    config.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get upgrade -y
        echo "10.0.10.100    master01" >> /etc/hosts
        echo "10.0.10.101    worker01" >> /etc/hosts
        echo "10.0.10.102    worker02" >> /etc/hosts
    SHELL

    config.vm.define "master01" do |master01|
        master01.vm.hostname = "master01"
        master01.vm.network = "private_network", ip: "10.0.10.100"
        master01.vm.provider "virtualbox" do |vb|
            vb.memory = 4096
            vb.cpus = 4
        end
    end

    (1..2).each do |i|
        config.vm.define "worker0#{i}" do |node|
            node.vm.hostname = "worker0#{i}"
            node.vm.network "private_network", ip: "10.0.10.#{i+100}"
            node.vm.provider "virtualbox" do |vb|
                vb.memory = 2048
                vb.cpus = 2
            end
        end
    end

end
