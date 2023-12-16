

On-Premises Information system architecture (not cloud)
=
#### layer 1 - physical facility

this includes something like a building, data room, coolling system/environment control

all of this requires space which includes cost of space, physical security, and employees to run everything or repair things


#### Layer 2- physical infrastructure

this includes server racks, servers, cabling, computers, power, network, storage

costs alot to purchase and maintain

#### Layer 3 - Virtualization

Like VMware or Hyper-V running on the infrastructure
requires a lot of maintenance of platforms and licensing for platforms

#### Layer 4 - virtual machines

operating systems running on virtualization

#### Layer 5 - Services

#### Layer 6 - Workload


The Cloud
=
the cloud by nature takes care of layer 1 through to 3, thus reducing costs massively while still providing the same needs.

This is provided at a scale and with redundancy 

cloud has an extra added layer called a management plane inbetween the first 3 layers and the upper layers


Lift and shift
=
the action of moving on premise services/workloads/virtual machines to the cloud


Cloud Services
=
the lower you go the more control you have, the higher you go the increase ease of administration you have

### Infrastructure as a Service

moving a virtual machine to the cloud, would mean to switch to the infrastructure as a service (iaas) model

you're managing your own virtual machines, but they are stored on the cloud

### Platform as a service

you no longer are managing your own virtual machines or hosting environment
, they are managed by the provider

they provide a platform to host your application 

like hosting a website or database in the cloud instead of on premise servers

used for hosting your workload managed by you

### Software as a Service

cloud provider manages the entire workload like Microsoft 365 or gsuite, salesforce


Accessing cloud services
=

the cloud is just like a WAN

- public endpoints
- gateways (vpn tunnels/tunneled communications)
- private circuit communication