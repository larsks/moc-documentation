## Kaizen Bare Metal

### Kaizen bare metal services
 -  Each node has 2 nics labelled nic1 and nic2. Currently there are 42 total dell nodes available; more will be added in future.
 -  Currently the nodes don't have a lease time, but a leasing system will be added soon.
 -  For provisioning, we connect `nic1` to the bmi provisioning network natively using HIL.
 -  If you require a public IP address for your node, talk to Naved/Rado.

### Using HIL
HIL allows users to reserve nodes and connect them via isolated networks.
 1. Logon to the kzn-hil-client (available over Internet).
  `ssh username@kzn-hil-client.infra.massopen.cloud` (Helpful tips about SSH available in the last section of this document)
  Alternatively, you could also install a client locally. If you have pip (or in python virutalenv) run
  `pip install git+https://github.com/cci-moc/hil`
  and `export HIL_ENPOINT=https://kzn-hil.massopen.cloud`
  (If you are going to be using BMI, the BMI machine also has a HIL client configured
  to talk to kzn-hil).
 1. Export your HIL credentials that an admin should have given you. In bash, do this
```shell
export HIL_USERNAME=username
export HIL_PASSWORD=password
```
  You could put these in your bashrc so they are automatically in your environment
  when you login (you could figure out it's equivalent if you are using a different shell).

### Basic HIL commands
 -  `hil project node list <project-name>`
  This will list all the nodes that are currently added to your project.
 -  `hil node list free`
  will list all nodes that are available.
 -  `hil project node add <project-name> <node-name>`.
  This will add the node to your project.
 -  `hil project node remove <project-name> <node-name>`.
  This will remove the node from your project. Before running this command, make
  sure that you remove all networks first and disable OBM.
 -  `hil node show <node-name>`
  This will show you the information about your node.
 -  `hil node network connect <node-name> <nic-name> <network-name> <channel>`
  to connect your node's nic to a network. Channel could either be `vlan/native`
  or `vlan/<vlan-id>`.
 -  to find the vlan-id of a network, run `hil network show <network-name>`
 -  `hil node network detach <node-name> <nic-name> <network-name>`
  this will remove <network-name> from your node's nic.
 -  `hil node obm enable <node-name>` to enable OBM which lets your perform all
  power commands, and view console.
 -  `hil node obm disable <node-name>` to disable OBM.
 -  `hil node power cycle <node-name>`.
  to power cycle a node.
 -  `hil node power off <node-name>`.
  to power off a node.
For more information about HIL, checkout the [HIL Repo](http://github.com/cci-moc/hil)

### Using BMI
BMI allows you to provision software on your nodes. Once your nodes are reserved using HIL
you can use BMI to pxe boot your node.

*Note: BMI uses diskless provisioning, so your changes are not saved to the local disk. Your disk is
saved in ceph that BMI automatically manages.*

 1. Logon to the BMI machine via HIL client.
  `ssh username@kzn-vbmi01res.infra.massopen.cloud`
  or use its ip address which is 172.16.96.4
 1. Once logged in, export your HIL credentials explained above.
 1. Run this command to see a list of available images. The project name is the same name as in HIL.
  `bmi ls <project-name>`
 1. If this is the first time you got a node, and you want to provision it with an OS using BMI.
  Run this command:
 `bmi pro <project-name> <node-name> <image-name> nic1`
      -  Make sure to connect the network `bmi-pro` to your node's nic1.
      -  The command should return `success` within 5-10 seconds.
 1. Once you provision your node, power cycle your node using HIL and it should boot
  into your image in 3-4 minutes.
 1. ssh to your nodes using the hil names. So if you provisioned node neu-1-2; you'll do `ssh root@neu-1-2`
 1. To deprovision a node, run this:
  `bmi dpro <project-name> <node-name> em1`
      -  This command should return `success` within 5-10 seconds. This will **delete** your
      image.

For more information about BMI, checkout the [BMI repo](https://github.com/cci-moc/ims)

### How to SSH
This section can be skipped if you already know how to ssh like a pro. 
It's intented to simplify ssh for linux users. Windows user can use putty to ssh.
 1. Create a `config` file in your `.ssh` directory, and edit & paste the following:
```shell
Host kzn
    User <username>
    Hostname kzn-hil-client.infra.massopen.cloud
    ForwardAgent yes

Host kznbmi
    User <username>
    Hostname kzn-vbmi01res.infra.massopen.cloud
    ProxyCommand ssh kzn -W %h:%p
```
  Make sure you have ssh agent forwarding on.
  Run `ssh-add -l` to see list of available identities, if it doesn't display anything
  then run `ssh-add -k` it will print "Identity added: /path/to/key"
 1. Now from a local terminal, type `ssh kumo` to get to the kumo-hil-client.
 1. From another local terminal, type `ssh kumobmi` to get to kumo-bmi machine.

### How to connect your nodes to internet
In kaizen, your nodes connect to the internet using the provisioning network so no additional
configuration is required!

### How to connect to the local RHEL server for packages
 1. visit `mochat.massopen.cloud/repos` using lynx from any kaizen or kumo machine/vm.
 1. Download whatever files are useful and put them in `/etc/yum.repos.d/` of your node.