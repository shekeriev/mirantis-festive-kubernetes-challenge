# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false

  config.vm.box = "shekeriev/debian-12"
  
  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
    v.cpus = 2
  end

  (1..3).each do |i|
    config.vm.define "node-#{i}" do |node|
      node.vm.network "private_network", ip: "192.168.99.10#{i}"
      node.vm.hostname = "node#{i}.k0s.lab"
    end
  end

end
