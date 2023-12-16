
Fingerprinting web applications

### Nmap for fingerprinting

`sudo nmap -p80 -sV 192.168.50.20` - after finding an open port running on port 80 we can further enumerate (fingerprint) the service and take note of the information.

`sudo nmap -p 80 --script=http-enum <ip>` - further enumerate and fingerprint the web server - may reveal interesting directories


### Technology Stack Identification

We can use wappalyzer to reveal and identify the tech stack running on the webserver.
This can be used both as a website and a browser extension. Take note of the information collected by this application.


### Directory Brute-forcing

Enumerating web directories by brute-force can produce a lot of traffic so it is not very useful when we are trying to stay covert. It can still be used though, we reduced threads when used to reduce the amount of traffic generated.

`gobuster dir -u 192.168.50.20 -w /usr/share/wordlists/dirb/common.txt -t 5` - brute-fore the directory about using the common wordlist. `-t 5` sets the amount of threads to use, the default for gobuster is 10.


### Burp Suite

Contains tools used for manual testing via proxy. commercial version contains vulnerability scanning capabilities and more. Zap is unrated and contains vuln scanning for free.

