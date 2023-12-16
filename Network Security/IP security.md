
secure networking protocols are implemented at layer3, layer 4 or at a higher layer, layer 7

# Basics

layers 3 and 4 are usually in-built in an operating system.

Ip security can be implemented in a network device and hence out of control of the sender
protection via a dedicated tcp port needs to be implemented in a sender

IPsec:
- IETF sec protocol
- layer 3
- end entities negotiate securely in real time to establish a secure communications channel by (using IKE protocols):
	- authenticating each other
	- establishing session keys
		- message origin authentication
		- confidentiality protection


# Services 

IPsec Sec Services:
- Access control
- connectionless integrity
- data or message origin authentication
- rejection of replayed packets
- confidentiality
- limited protection against traffic analysis

IP security provides methods to implement secure transport between two end entities:
- transport between two end entities
- tunnel between two network sites

Secure networking between entities or sites:
- PN (leased line), at physical layer, expensive, isolated (secure)
- VPN (secure tunnel), protecting whole message (header and contents), may emulate leased line


# IPsec Headers

generic format of IP packet after it is processed by IPsec

![[Pasted image 20230823131610.png]]

## AH (authentication header)

provides most IPsec services, except confidentiality 

## ESP (Encapsulating Security Payload)

Provides all IPsec Services.
Integrity protection does not protect the outer IP header (if any).

# Security Associations

## Overview

- refers to the set of security parameters of an IPsec-secured connection
- Used to create a one-way secure connection.
- Identified by its security parameter index (SPI)

both sides store the SA parameters for the secured connection
these parameters are used during the operating of the secure connection for the security services provided by IPsec.

## Parameters

- sequence number counter
	- 32 bit counter to generate  number for the sequence number field in header (0 - 2^32 -1)
	- timing and order of IP packets
- sequence counter overflow
	- a flag - indicates if overflow of sequence number counter should generate an auditable event and stop further transmission of packets using this SA
	- if terminated a new SA needs to be created to continue secure information
- anti-replay window
	- used to determine whether an incoming IPsec packet is a replay
	- it allows for transmission delays but replayed packets cannot be detected within the window
- AH information
	- message authentication algorithm
	- keys, key liftimes and related parameters used by AH protocol
- ESP information
	- Encryption and message authentication algorithms
	- keys, key lifetimes
	- initial values
	- ESP related parameters
- Lifetime of this Security Association
	- when it expires, SA is terminated
- IPsec Protocol Mode
	- Tunnel or
	- transport
	- wildcard (mode is determined during actual operation of secure connection)
- Path MTU
	- any observed path maximum transmission unit

# Modes of Operation

## Transport mode

ESP ENCAPSULATES IP PAYLOAD

- provides secure transport
- only protects message contents (packet payload)
- does not protect other information (IP header)

## Tunnel mode

ESP ENCAPSULATES IP HEADER AND IP PAYLOAD

- provides secure tunnel
- protects the whole ip packet including IP header and payload
- needs to add a new IP header since original is hidden by encryption
- usually used by firewalls/router to protect network traffic
- protects original end entities against traffic analysis as it hides the original IP header if encryption is used

# ESP Encapsulation Packet Format

![[Pasted image 20230823152456.png]]

- Padding - used for block cipher (DES, 3DES or AES), also protects against cryptanalysis especially for small data size or traffic analysis to hide payload size
- authentication data - keyed-hash created from the payload data and the session key

# Firewall & Routers

## Firewall

firewalls need to look at layer 4 information to perform packet filtering
transport layer segment is usually the payload of an IP datagram - encrypting the IP datagrams payload will prevent firewalls from access to layer 4 information

## Router

Network addresss port translation (NAPT) (Cisco PAT) wont work with ESP encryption
NAPT - used to translate internal IPs to 1 external public IP, TCP port number and some other parameters may need to be changed, but the router cannot alter values protected by IPsec encryption

# Advances Features

![[Pasted image 20230823154707.png]]


# Anti-Replay

![[Pasted image 20230823155904.png]]

protection:
- 32 bit number is placed in each outgoing IPsec packet
- sliding window is used by receiver to keep track of received packets
	- packets may arrive out of order, smaller sequence number may arrive after larger sequence number - within the window this is allowed (replay attacks can still happen within the window)

False out-of-order arrivals happen:
- if the sequence counter overflows (starts from zero again)
- an IP sec packet with a smaller sequence number always arrives later than the IPsec packet with the largest sequence number
- An IPsec SA can be configured to be terminated when the sequence counter reaches its maximum value - and a new IPsec SA is to be established for secure communication to continue

# Typical IPsec system

in addition to typical IPsec components at typical IPsec implementation should also have:
- Security Policy Databse (SPD) (Cisco called rule-base)
- Security Association Database (SAD)

the relation between these databases - logically outbound and inbound traffic have their own databases

![[Pasted image 20230823161700.png]]

## SPD

typical SPD entry has:
- Id number for entry in the database
- selectors - match a packet (including src/dst ip, protocol, src/dst ports, etc.)
	- action is performed if these selectors are matched to the parameters in a packet and other outside relevant parameters

example actions:
- dropped
- forwarded or accepted (with or without IPsec)
	- and if IPsec, which SAs

Typical format:

![[Pasted image 20230823162651.png]]

outbound traffic processing
1. IPsec searches SPD for an entry whose selectors match the values in TCP/IP headers and other params if any
2. if a match is found and protection required
	1. SAD database is searched for the SA to implement the SPD action
	2. If SA entry related to SPD entry is located in SAD database packet is processed by IPsec according to the entry
	3. if no SA to implement actions specified in SPD entry
		1. IKE protocol is used to create the SA

other outcomes for the search of SPD depend on how the SPD is designed and if other filtering is implemented.
for an incoming packet a similar search process is required.

## SAD

- more dynamic in nature since SAs are created and removed during an IPsec operation
- logically there is one SAD for inbound and one for outbound traffic

SAD entry should have:
- entry id
- in some implementations the actual SA for this SAD entry may be stored in another database, referred to SA list ( instead of containing the details of an SA will have a pointer to SA on SA list)

# Windows IPsec Configurations

- IP security Policy Management snap-in (Microsoft management console mmc.exe)
	- IKE settings (lifetimes of policy and session keys for the IKE)
	- list of cryptographic methods used to protect the IKE
	- Access control - traffic selector
	- protection methods - list of cryptographic methods for protecting the IPsec secure channel and other params
	- IKE authentication method


# Examples of network security layers

example of layer 7 security: PuTTY, openvpn, winscp
layer 3: tunneling
layer 4: tls/ssl


# Benefits

protection implemented at the layer 3 can:
- protect all internet protocols and even IP itself
- be transparent to the upper layer protocols and applications
- be in a network device e.g. transparent to individual users/computers

incorporation of security into layer 3 or 4 
	may require modifications to the OS
	automatically causes or forces applications to be protected without major modifications to the applications, especially for layer 3


# layer 4

only protects the particular transport-layer application/service
	only the TCP service using the particular TCP port allocated to the protocol






