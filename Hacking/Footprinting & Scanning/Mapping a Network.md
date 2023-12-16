Scope

Discovery

Process of mapping
1. Physical access
	1. Physical Security
	2. OSINT
	3. Social Engineering
2. Sniffing
	1. Passive Recon
	2. Watch network traffic
3. Arp
	1. Resolve IP to MAC addresses
4. ICMP
	1. Traceroute 
	2. Ping


Tools:
**Wireshark** - Monitor network traffic

**Arp-scan** - Discovers devices on a network, maps ip address and mac addresses 
example: **sudo arp-scan -I eth0 -g 10.10.10.0/24**

**Ping** - echo request to hosts, check if they're up

**Fping** - pings multiple hosts at one time
example: fping -I eth0 -g 10.10.10.0/24 -a 2>/dev/null
	a - alive
	I - interface

**nmap** - port scanning

**zenmap** - gui nmap - has extra stuff like topology graph

Use multiple of these tools