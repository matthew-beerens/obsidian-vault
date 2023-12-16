
## Internet of things

A vision to connect everything with the internet using wireless technologies, wifi, bluetooth.

A platform to connect every day things embeded with software/sensors/electronics to the internet to collect and exchange data

should:
- sense their surroundings
- communicate cheaply
- act based on what we need adn like without explicit instructions


## Building blocks

- sensors (collect data)
- identifiers (of data sources)
- software (analysis of data)
- Internet connectivity (communicate and notify)


## Main characteristics

- most object or things have or can have sensing or actuation and communication capabilities (may also have other data processing or retention capabilities)
- capability of direct machine-to-machine communication control over the internet

## Monitor and control

- all things need to be (to monitor things from anywhere in the world):
	- uniquely identifiable to
	- communicate between things and 
	- ability to sense specific information about the the thing (sensors)
	- a medium to communicate


## Security

- have limited hardware and security
- issues of physical security (IoT in insecure environments)
- Data transfers from outside organisations
- Data access (encryption)
- more IoT devices coming online = cyber attacks greater potential impact


## Connectivity

typically low-power wireless technologies
- 802.15.4 WPAN
- 6LoWPAN (IPv6 over low-power WPAN)
- 802.11ah extension (Wi-Fi HaLow)
- Bluetooth Low Energy (BLE)
- RFID
- ZigBee
- NFC
- 802.11a, cellular, ethernet also in the mix

## Wireless Sensor Networks (WSN)

- class of ad hoc networks
- reliable monitoring of environments
- small disposable low power devices
- sensor node converts a sensed attribute into a form suitable for communication and transmission

## Fixed WSN

- sensors places at fixed locations linked by a wireless network to perform distributed sensing tasks
- useful for continuous and regular monitoring
- communication methods:
	- LOS infrared
	- wireless RF communications

## Radio Frequency Identification (RFID)

- uses radio waves to track and identify people, animals, objects, shipments
- done by principle of modulated backscatter
	- reflection of the radio waves striking the RFID tag back to the transmitter source with its stored identification information

## Wearable devices

detect biometric information:
- shoes
- watches
- glasses
- belts

Smart device collect the information and communicate with other devices, control center or medical server using the available networks or the internet.

## Challenges

- most IoT devices:
	- small memory
	- limited processing power
	- limited communication capabilities
	- energy constraint
- requires integration of multi-technology networks
- ipv4 and ipv6 issues (management and scalability)
- scale (ipv6)
- security: compromised devices make for a launching pad for attacking other devices
- privacy: who owns data, how to control whether it is shared and with who
- big data and data analytics: massive amounts of data from different sources in various forms
- interoperability: protocols and architectures are based on proprietary elementts and other are open

## Secure Communications

Many functions:
- authentication
- encryption
- intrusion detection

random placement of things makes it impossible to predict the nature of adjacent sensors or those within communication range pushing the need for keys to encrypt data

Its common to use symmetric keys in WSN but sharing common key between two communication sensors is difficult as the sensors spread out randomly


## Summary 

- IoT is all around us
- IoT as the network of things, with clear element identification embedded with software intelligence, sensors and ubiquitous connectivity to the internet
- IoT system is made up of
	- sensors to monitors events
	- actuators to influence the environment
	- hardware to create the platform and its connections
	- software to provide a framework to execute processes
- goal is to create a better environment for humanity
- securing IoT is viewed as a challenge