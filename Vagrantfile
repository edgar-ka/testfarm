# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|

  config.vm.define "control" do |an|
    an.vm.box = "generic/debian10"
    an.vm.network "private_network", ip: "192.168.11.100"
    an.vm.hostname = "control"
    an.vm.provider "libvirt" do |lv|
      lv.memory = 2048
      lv.cpus = 2
    end
  end

  (1..3).each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.box = "generic/debian10"
      node.vm.network "private_network", ip: "192.168.11.10#{i}"
      node.vm.hostname = "node#{i}"
      node.vm.provider "libvirt" do |lv|
        lv.memory = 2048
        lv.cpus = 2
      end
    end
  end 

end
