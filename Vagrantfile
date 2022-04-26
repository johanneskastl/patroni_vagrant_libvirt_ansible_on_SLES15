# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define "patroni" do |node|

    # which image to use
    node.vm.box = "SLE15-SP3"
    node.vm.box_check_update = false

    # sizing of the VMs
    node.vm.provider "libvirt" do |lv|
      lv.random_hostname = false 
      lv.memory = 2048
      lv.cpus = 2
    end

    # set the hostname
    node.vm.hostname = "patroni"

  end # config.vm.define

end
