
lower level of service

provides physical facility, physical infrastructure and virtualization for you to run your own virtual machines and greater on

### Main Components

virtual-network/Virtual private cloud

virtual machines/EC2, (and associated storage, built-in disks, memory, virtual cpus,  Images)

can have more than one virtual machine connected to the virtual network, the network is all software defined 

virtual network may have public endpoints or gateways or load balancers 

### Virtual Network

network encompassing subets

v-net defines:
defining dns settings, overall ip range
certain sercurity settings
gateways (vpns, private circuit)
load balancers
virtual appliances  ike firewalls 

subnets within v-net define:
	IP range
	access control (network sercurity groups or access control lists)
	routing is handled for you most of the time


### Virtual Mahines/EC2

1. define series and size of instance
	1. series use particular equip like different processors, or continuous or burstable performance, virtual CPUs, memory
2. Image its based on
	1. Operating System (windows or linux)
	2. Software (like LAMP stack or Django)
	3. Custom Images
3. Storage
4. Security & Access (main user account, password, keypair, networking rules, etc.)
	1. lots of overlap with v-net


## Storage

storage can be apart of an instance or separate form the instance as part of its own infrastructure service or platform service

