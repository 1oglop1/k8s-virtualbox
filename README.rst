k8s-virtualbox
--------------

I'm messing around k8s and tectonic installation.
The description is very vague but should be enough for replication.

My findings:

system must have curl and sed
https://github.com/coreos/tectonic-docs/blob/master/Documentation/install/bare-metal/metal-terraform.md


Virtualbox setup
----------------

1. Download Vbox
2. install extension: VirtualBox Extension Pack
3. Create machines

   1. Boot order HDD, Network
   2. Network: 1. Bridge or Host Only(depends on network), 2.NAT for internet connection
   3. You must use this order otherwise PXE does not work.

prepare matchbox
----------------
1. downlaod matbox installer
2. generate certificates ( follow official docs )
3. prepare matchbox docker (docker-compose)
4. copy certificates ``ca.crt, server.crt, server.key`` to correct location ``docker-stuff/volumes/etc/matchbox``
5. Download CoreOS using installer script ``get-coreos stable <version> .``
6. Copy downloaded ``coreos`` directory to ``docker-stuff/volumes/var/lib/matbox/assets``
7. run ``docker-compose up matchbox``


prepare dnsmasq
---------------
DNS entries needed:

k8smaster.home
k8s.home (point to any node according to docs)
k8snode1.home

I'm using dns masq as DHCP proxy just for iPXE.

1. cd ``docker-stuff`` and ``docker-compose up dnsmasq``

Tips:

* If something does not work, check official docs https://github.com/coreos/matchbox/tree/master/contrib/dnsmasq
* If you are using ubuntu beware of this: https://github.com/coreos/matchbox/issues/708

Tectonic installer
------------------

Installer will need ssh key, use ssh-add to add the key to the machine where is it running from!

1. download installer
2. follow the official docs for bare metal https://coreos.com/tectonic/docs/latest/install/bare-metal/index.html#4-tectonic-installer

Then at the end you can hit Submit, which will trigger terraform apply

3. Start VMs and let them boot from iPXE


Terraform (probably not needed if using web installer only)
----------
Download terrafrom and matbox-provider
https://www.terraform.io/downloads.html
https://github.com/coreos/terraform-provider-matchbox/releases

Or use TF and provider shipped with tectonic installer



Terraform without web UI
------------------------
Download terrafrom and matbox-provider
https://www.terraform.io/downloads.html
https://github.com/coreos/terraform-provider-matchbox/releases

1. cd assets/{cluster name}
2. terraform init platforms/metal
3. terraform plan platforms/metal
4. terraform apply platforms/metal
    4.1 Leave it running and turn on VMs until they get stuff from iPXE


