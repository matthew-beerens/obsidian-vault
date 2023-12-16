
traditional network security types:
- ips, 
- ids, 
- firewalls 
- vpns
- anti-malware
- access control

You need to know where you are within the network, are you behind firewalls, is your target behind a firewall, what is the topology like, then try to see from the targets perspective, what do they see

## passive attacks

adversary adopts the role of the receiver
involves observing or copying information transmitted between two entities
this includes unauthorized release of message contents and traffic analysis
hard to detect, normally detected later on when harvested information is used

## active attacks

the adversary adopts the role sender

**masquerade attack**: pretending to be someone else, and sending messages as another identity (spoofing mac or ip) could lead to modification of information target of access to target information/resources, hides real origin of message

**replay attack**: makes reuse of a previously sent/intercept message, to the reciever or the sender(reflection attack)

**modification attacks**: intercepts a message, modifies it and then forwards to receiver, this also includes modifying the message stream eg reording or deleteing messages in a sequence of related messages. bi directional modification attacks are sometimes called a mitm attack

**man in the middle attacks**

**arp cache poisoning** - changing addresses in an arp cache of a machine to redirect network traffic through you


## Secure Communication Requirements

- end entity authentication
- message integrity
- message origin authentication
- privacy or confidentiality authentication
- additional requirements depending on org or individual

## Security Services

CIAAAA : confidentiality, Integrity, Authenticity, Availability, Accountability, Auditing

#### network security requirements
CIA: three most basic security services, Confidentiality, Integrity, Authenticity 

#### System security requirements
CIA+AAA = CIAAAA

## Datalink 

- on a broadcast lan, in theory, every host attached has access to every frame being transmitted
- Passive attack are possible by using promiscuous mode within the NIC
- frame data (payload) can only be encrypted as a whole at this layer, routers will need to decrypt to find information for forwarding further

## ARP Cache Poisoning

this can be achieved by:
- updating the ARP cache of the target computer by replacing the default gateways mac address with your own
- updating the default gateways ARP cache by changing the target computers ip address mac addressing mapping to your mac address.

1. make an ARP quiery with with the Target IP you want to impersonate as the source IP and your mac address or a spoofed one as the source mac, send this query to the target you want to update the cache table for by setting it as the destination IP
2. the target (destination ip) will reply and also updates its arp cache using the information in your crafted query

## Network Layer

IP - encapsulates tcp degment data, during transmissions a large payload will be fragmented and carried in smaller ip blocks

#### vulnerable to:
- packet sniffers
- packet injection: sending useless packets to jam communication/carrying out normal operations

#### IP Spoofing
useful to impersonate another machine or to gain trust or to hide source of the packet, responses will be sent to the spoofed address though.

## DoS Attacks

- ping of death - single crafter ICMP packet to crash a computer
- coordinated or distributed ping attack (DDoS)
- Smurf Attack - IP spoofed ICMP echo request packets, multiplies on networks to create a large number of OCMP packets to the target

## Transport layer

encapsulated application data (application, session presentation)
header includes sequence number, acknowledge number, HLEN, code bits (SYN, ACK), window, checksum, urgent pointer, options

#### TCP

operates with a three way handshake

###### SYN Flooding

requires: spoofing of ip address
half open connections can be made by sending a syn packet to a service, which the server needs to remember after responding to the attacker with a SYN,ACK.
after an amount of time the half open connection will be dropped, but if a large number of syn packets are sent from the attacker to the target the attacker can overflow the tempory storage for half open connections which will then cause the service to refuse new connections. this does not affect already established TCP connections (obviously).

###### SYN Flooding defence

- firewalls
- flooding packets always assumed to be spoofed
- use of cookies to overcome ip spoofing
- intermediate devices to detect and block ip spoofed packets
- host and network IPS/IDS