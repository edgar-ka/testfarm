# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
ENV["VAGRANT_NO_PARALLEL"] = "yes"

Vagrant.configure("2") do |config|

  config.vm.define "control" do |an|
    an.vm.box = "generic/debian10"
    an.vm.network "private_network", ip: "192.168.11.100"
    an.vm.hostname = "control"
    an.vm.synced_folder ".keys/", "/mnt/keys"
    an.vm.provider "libvirt" do |lv|
      lv.memory = 2048
      lv.cpus = 2
    end
    an.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus = 2
    end
    an.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get -y install ansible mc git
    useradd -G sudo -s /bin/bash --create-home ansible
    sudo -u ansible ssh-keygen -q -t rsa -N '' -f /home/ansible/.ssh/id_rsa <<<y 2>&1 >/dev/null
    for k in $(ls /mnt/keys/*.pub)
      do
        cat $k >> /home/ansible/.ssh/authorized_keys
      done
    chown ansible:ansible /home/ansible/.ssh/authorized_keys
    chmod 0400 /home/ansible/.ssh/authorized_keys
    cp -f /home/ansible/.ssh/id_rsa.pub /mnt/keys/control.pub
    echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config
    echo "ansible        ALL=(ALL)       NOPASSWD: ALL" > /etc/sudoers.d/ansible
    echo "127.0.0.1 localhost\n192.168.11.100 control\n" > /etc/hosts
    for h in {1..3}
      do
        echo "192.168.11.10$h node$h" >> /etc/hosts
      done
    echo "[nodes]\nnode[1:3]" > /etc/ansible/hosts
    SHELL
  end

  (1..3).each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.box = "generic/debian10"
      node.vm.network "private_network", ip: "192.168.11.10#{i}"
      node.vm.hostname = "node#{i}"
      node.vm.synced_folder ".keys/", "/mnt/keys"
      node.vm.provider "libvirt" do |lv|
        lv.memory = 2048
        lv.cpus = 2
      end
      node.vm.provider "virtualbox" do |vb|
        vb.memory = 2048
        vb.cpus = 2
      end
      node.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get -y install mc git
      useradd -G sudo -s /bin/bash --create-home ansible
      sudo -u ansible mkdir /home/ansible/.ssh
      chmod 0700 /home/ansible/.ssh
      for k in $(ls /mnt/keys/*.pub)
        do
          cat $k >> /home/ansible/.ssh/authorized_keys
        done 
      chown ansible:ansible /home/ansible/.ssh/authorized_keys
      chmod 0400 /home/ansible/.ssh/authorized_keys
      echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config
      echo "ansible        ALL=(ALL)       NOPASSWD: ALL" > /etc/sudoers.d/ansible
      echo "127.0.0.1 localhost\n192.168.11.100 control\n" > /etc/hosts
      for h in {1..3}
        do
          echo "192.168.11.10$h node$h" >> /etc/hosts
        done
      SHELL
    end
  end 

end
