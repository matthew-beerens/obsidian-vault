
# Basics

we can use nmap to perform host discovery:

```
sudo nmap <ip>/24 -sn -oA discovery | grep for | cut -d" " -f5
```

we can also scan multiple IPs with `-iL` or just by providing space separated IPs or 
providing an ip range (e.g. 192.169.1.1-10).

sometimes the scan wont work because of an intermediary firewall ignoring specific requests.

nmap will send an ARP request by default before an echo ping request. To ensure an echo ping request is sent we can use the `-PE` flag. It might also be needed to use `--disable-arp-ping` as well.

we can use `--packet-trace` to show all the packets sent and received. 

we can also use `--reason` to check why a host was marked alive by nmap.

ICMP echo requests can help us determine if the host is a live and also determine the operating system.