# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
num_instances=2
instance_name_prefix="calico"
primary_ip = "192.168.74.101"


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "bento/centos-7.4"
  config.ssh.insert_key = true
  config.ssh.username = "vagrant"

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", 4096] # RAM allocated to each VM
    vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
  end

  # Set up each box
  (1..num_instances).each do |i|
    vm_name = "%s-%02d" % [instance_name_prefix, i]
    config.vm.define vm_name do |host|
      host.vm.hostname = "#{vm_name}.rohith.com"

      ip = "192.168.74.#{i+100}"
      host.vm.network :private_network, ip: ip

      # The docker provisioner installs docker.
      host.vm.provision :docker, images: [
          "busybox:latest",
          "gcr.io/google_containers/pause",
          "quay.io/calico/node:release-v3.2",
          "alpine:latest"
      ]

      # Setup the node
      host.vm.provision :shell, :path => "bootstrap.sh", args: [ "#{i}", "#{primary_ip}", "#{ip}", "#{vm_name}"]
    end
  end

end