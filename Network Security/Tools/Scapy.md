runs in its own scapy shell

`ans,unans=srp(Ether(dst=“ff:ff:ff:ff:ff:ff”)/ARP(pdst=“192.168.0.1”), timeout=2, iface=“eth0”`) - build and send an ARP query
	ans,unans - output parameters of srp (ans contains ARP reply if successful, unans contains packets with no replys)
	srp - used to send packets (specified in the first param)
	/ - stacking operator (above example encapsulates arp pdu within ethernet frame)
	dst - mac address destination
	pdst - ip address destination
	timeout - max time to wait for reply
	iface = interface to use/send packets out

`ans.show()` - show the replied packet

crafting a IP packet:
1. `a = Ether()/IP(dst=“192.168.0.1”)`
2.  `sendp(a)` - if replied arnt wanted
3. `srp(a)` - if replys are wanted

ICMP packet:
1. `b = Ether()/IP(dst=“192.168.0.10”)/ICMP()`
2. `sendp(b)`

TCP segment:
1. `c = Ether()/IP(dst=“192.168.0.3”)/TCP(sport=1234, dport=80)`
2. `sendp(c)`

helpful commands:

`ls(packet)` - list the contents of a packet