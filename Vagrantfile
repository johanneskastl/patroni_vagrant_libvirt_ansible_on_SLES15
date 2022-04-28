# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  ###################################################################################
  # haproxy node
  config.vm.define "haproxy" do |node|

    # which image to use
    node.vm.box = "SLE15-SP3"

    # sizing of the VMs
    node.vm.provider "libvirt" do |lv|
      lv.random_hostname = false
      lv.memory = 1024
      lv.cpus = 1
    end

    # set the hostname
    node.vm.hostname = "haproxy"

  end # config.vm.define

  ###################################################################################
  # define number of etcd nodes
  E = 1

  # provision E VMs as nodes
  (1..E).each do |i|

    # name the VMs
    config.vm.define "etcd#{i}" do |node|

      # which image to use
      node.vm.box = "SLE15-SP3"

      # sizing of the VMs
      node.vm.provider "libvirt" do |lv|
        lv.random_hostname = false
        lv.memory = 1024
        lv.cpus = 1
      end

      # set the hostname
      node.vm.hostname = "etcd#{i}"

    end # config.vm.define nodes
  end # each-loop over all VMs

  ###################################################################################
  # define number of patroni/postgres nodes
  P = 2

  # provision W VMs as nodes
  (1..P).each do |i|

    # name the VMs
    config.vm.define "patroni#{i}" do |node|

      # which image to use
      node.vm.box = "SLE15-SP3"

      # sizing of the VMs
      node.vm.provider "libvirt" do |lv|
        lv.random_hostname = false
        lv.memory = 4096
        lv.cpus = 2
      end

      # set the hostname
      node.vm.hostname = "patroni#{i}"

      # if this is the last machine
      if i == P

        #################################################
        # only target one node to not run ansible twice
        # but do not limit this to this node (set ansible.limit to all)
        node.vm.provision "ansible" do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.groups = {
            "patroni_nodes"  => [ "patroni1", "patroni2" ],
            "etcd_nodes"  => [ "etcd1" ],
            "haproxy_server"  => [ "haproxy" ],
          }
          ansible.playbook = "ansible/playbook-vagrant.yml"

        end # node.vm.provision
      end # if-condition last machine
    end # config.vm.define nodes
  end # each-loop over all VMs
end
