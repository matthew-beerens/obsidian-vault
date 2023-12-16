Discover open ports and services running on a target using namp

default nmap scan: 
**nmap <ip> 
performs a syn scan, if target is windows, add -Pn flag, windows filters icmp
-v shows ports as they are discovered 
-A (aggressive flag) - -sV -O -sC all run with -A