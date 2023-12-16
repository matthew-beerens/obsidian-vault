
systems may be connected to more than one network, or a single network, host discovery should still be conducted

## Port Forwarding & routing 

redirecting traffic from a target system port to a port on our system in order to interact with the internal service that we could not interact with before

#### Meterpreter

1. ipconfig - check for interfaces
2. run autoroute -s netword_id/cidr - run this on interface where our target ip address is
3. run autoroute -p - to display active routing table
4. scanner/portscan/tcp - make use of this to scan other machines that our target can contact but we can
5. portfwd add -l localport -p remoteport -r targetip - forward remote port to our system so we can scan it with nmap and exploit it
6. use bind tcp to connect to target through exploit
7. once we have access rinse and repeat post exploitation steps

