# patroni_vagrant_libvirt_ansible_on_SLES15

This Vagrant setup creates all that you need to start playing with [Patroni](https://github.com/zalando/patroni/). This means we have three VMs running Patroni, postgreSQL and etcd and another VM running haproxy, loadbalancing your PostgreSQL cluster.

Patroni uses the etcd cluster, running on the same three nodes, to store the state of the PostgreSQL cluster.

Default OS is SLES15 SP3, but that can be changed in the Vagrantfile. Please beware, this might break the ansible provisioning which was only tested with SLES15 SP3.

(There is [another setup using openSUSE Leap 15.3](https://github.com/johanneskastl/patroni_vagrant_libvirt_ansible_on_openSUSE_Leap15) which is ~~very similar~~ almost identical to this one)

## Vagrant

0. Make sure your system meets the requirements for the VMs RAM. If not, feel free to reduce the size of the Patroni nodes (default `4096` MB).
1. You need vagrant obviously. And ansible. And git...
2. Fetch the box from [Suse](https://www.suse.com/download/sles/) by following the instructions on the page (TL;DR: download, `vagrant box add --name SLE15-SP3 SLE*Vagrant*.box`, done)
3. Make sure the git submodules are fully working by issuing `git submodule init && git submodule update`
4. Create a file `ansible/group_vars/all/SUSE_LICENSE_KEY.yml` containing a variable called `suse_license_key`, set to your SUSE SLES registration key. The file is ignored by git and will not get commited (unless you do something stupid...)
5. Add another variable `suse_ha_license_key` to the file `ansible/group_vars/all/SUSE_LICENSE_KEY.yml`, this time this is the registration code for the SUSE HA extension (which is needed for haproxy)
6. Run `vagrant up`
7. Open the haproxy node's IP address on port 80 to visit the haproxy statistics, including your postgresql backend.
8. Party!

## Disabling the Ansible provisioning

In case you do not want Ansible to install teleport (because you want to install it yourself), just comment out the following lines in the `Vagrantfile`:
```
        node.vm.provision "ansible" do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.groups = {
            "patroni_nodes"  => [ "patroni1", "patroni2", "patroni3" ],
            "etcd_nodes"  => [ "patroni1", "patroni2", "patroni3" ],
            "haproxy_server"  => [ "haproxy" ],
          }
          ansible.playbook = "ansible/playbook-vagrant.yml"

        end # node.vm.provision
```

## Cleaning up

In case you want to destroy the VMs and start from scratch, it might be a good idea to deregister the VMs. Running `ansible-playbook 90_SLES_deregister.yml` from inside the `ansible` directory should do the trick.
