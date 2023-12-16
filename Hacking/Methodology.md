
## host discovery

check /etc/hosts

1. `sudo arp-scan -I interface -g ip/cidr`
2. save ips in text file
3. `nmap -iL ips -sn`
4. `nmap -iL ips -Pn`

## Enumeration

on per service basis after step 1

1. proceed to run nmap scans with scripts and -sC `nmap -iL ip -p- -sV -sC`
	1. look for signs of vulnerability
	2. scan for vulnerabilities
2. try metasploit auxiliary modules if nmap scripts find nothing
3.  if no inherent vulnerabilites are found perform a bruteforce with hyrda
	1. if no credentials are found with a known username, try username as password, or two username lists
	2. try different wordlists

## Exploitation

1. if credentials are found, snoop around, try login to services, see what you find
	1. try password reuse
	2. try to bruteforce other services with the usernames found
2. if vulnerabilities are found attempt to execute them
3. If access is received continue to exploit and gain access through other services
	1. once all is done, perhaps edit system files to push along other vectors, for example phpmyadmin config files and restart services

