Ursula on Vagrant
=================

Vagrant is an excellent tool for easily testing out new tools or systems without having to install a bunch of crap to your userland.

Prerequistites
==============

* Virtualbox
* Vagrant
* Ansible ( 1.7ish )

Networking
==========

We've strived to keep the networking in the Vagrant install simple.  It _may_ not resemble what you would run in production,  but it does allow for an easier introduction to Openstack.

There are four network interfaces provided in the Vagrantfile.

eth0
----

Vagrant guest IP.  This isn't of any interest to us.

eth1
----

Host Networking,  This is the primary network for the openstack hosts and services.

eth2
----

Instance _Private_ Networking,  This is a single flat provider network that allows your instances to share IPs on the same network and uses the VM provider's gateway for routing.   This means that you should be able to access your instances from the regular network without having to enter/exit network name spaces.

There are plans to also allow tenant networks via VLAN/VXLAN tunnels which would also exist across this interface.  This is not yet implemented.

eth3
----

Instance _Public/Floating IP_ Networking  This interface is reserved for Floating IPs.   It is not currently being used but there are plans to enable floating IPs.

Using
=====

Vagrant can be used to stand up three types of environments:  allinone, standard, swift.

Do not use `vagrant up` directly we have wrapped a tool around it called `./bin/run_vagrant`.

The `run_vagrant` tool will bootstrap and provision your nodes.

1. It runs vagrant up on the required machines described in the Vagrantfile.
2. Vagrant reads `envs/vagrant/TYPE/vagrant.yml` for VM configuration for that stack.
3. It runs `ursula` against the nodes to provision openstack.
4. It does any final post-provisioning steps required.



allinone
--------

This will stand up a single monolithic Openstack VM.  It's much quicker than standard, but sacrifices HA and multi-node:

```
$ ./bin/run_vagrant up allinone
$ ./bin/run_vagrant ssh allinone allinone
$ ./bin/run_vagrant destroy allinone
```

standard
--------

This will stand up two controllers and a compute node.  It includes all the appropriate HA pieces and is a fairly good facsimile of a production install:

```
$ ./bin/run_vagrant up standard
```

swift
-----

This will stand up a multi-node swift cluster:

```
$ ./bin/run_vagrant up swift
```
