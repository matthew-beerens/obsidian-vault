Discover hosts within a network

**ping scan/ping sweep** - no port scan method for host discovery using -sn flag, will provied IP address of internal network
example: **sudo namp -sn 19.168.1.0/24** - discovers hosts on a local network

**netdiscover** - command tool to discover hosts on the network, uses arp
example: **sudo netdiscover -i eth0 -r 192.168.1.0/24** 