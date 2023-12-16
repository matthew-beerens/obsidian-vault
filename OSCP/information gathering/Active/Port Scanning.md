
### Netcat

we can use Netcat as a basic port scanner.

`nc -nvv -w 1 -z 192.168.50.152 3388-3390` - this will perform a tcp scan with a time out of 1 second within the given port range

`nc -nv -u -z -w 1 192.168.50.149 120-123` - this will perform a udp port scan with a time out of 1 second within the given port range. When udp ports are closed, they will respond with an ICMP port unreachable. This can show false positives as a lack of a response signifies an open port, but a firewall or rule could be dropping the packets. Open ports return by this command may not be open, and you will need to test which ones are open manually.

### Nmap

nmap has traffic rate limiting allowing more covert usage. RustScan and MASSCAN are both more concurrent and faster but produce a massive about of traffic and congestion.

##### Basic Nmap Usage

`sudo nmap -sS <target_ip>` - SYN "stealth" scan, doesn't complete the three-way handshake when scanning a target, meaning the information will not be sent to an application/application layer log. Modern day firewalls will still log this activity.

`nmap -sT <target_ip>` - TCP connect scan. takes longer, performs and waits for the complete tcp handshake. Sometimes we may need to use this due to lack of sudo privs or when scanning via certain types of proxies.

`sudo nmap -sU <target_ip>` - UDP scan. nmap will use the standard "ICMP port unreachable" response as well as sending protocol specific packets to known ports in hopes of a response, for detecting open and closed ports.

`sudo nmap -sU -sS <target_ip>` - Combination scan using UDP and SYN stealth scan. Used to get a clearer overall picture of the target. will reveal both open UDP and TCP ports.

`What we learn from one host we can extend to a network sweep. Starting with a broad scan and then moving on to more specific scans the more we discover`

`nmap -sn 192.168.1.1-253` - broad network sweep using nmap. This technique is more than just a ping sweep. nmap will send an ICMP echo request, TCP SYN to port 443, TCP ACK to port 80 and ICMP timestamp request to targets.

Greppable nmap network sweep:

1. `nmap -v -sn 192.168.1.1-253 -oG ping-sweep.txt` - we can save greppable output and grep the output of host IPs that are alive.
2. `grep Up ping-sweep.txt | cut -d " " -f 2` - grep hosts that are Up.

Greppable web-sweep:

1. `nmap -p 80 192.168.1.1-253 -oG web-sweep.txt` - scan all hosts on the network for a specific service running - tends to be more accurate than network sweeping.
2. `grep open web-sweep.txt | cut -d " " -f 2` - greps the IPs of hosts that are up and running.

Top-port sweep:

`nmap -sT -A --top-ports=20 192.168.1.1-253 -oG top-port-sweep.txt` - This is a top port sweep, used to save time and network resources - scans the top ports of all hosts on the network. these top ports can be found `/usr/share/nmap/nmap-services` file. `-A` enables OS version detection, script scanning and traceroute.

`sudo nmap -O 192.168.1.100 --osscan-guess` - This scan will guess the operating system if the fingerprinting isn't completely accurate.

`OS fingerprinting isnt always accurate due to firewalls or proxies rewriting packet headers`

`Banners can also be changed by administrators to mislead potential attackers (security through obscurity)`

`nmap -sT -A <target>` - Typical nmap scan including banner grabbing.

##### Nmap Scripting Engine (NSE)

`--script-help` - use with any nse script to get help on how to use the script.
Without internet access, help information can also be found in the script itself.

`nmap --script http-headers <target>` - This script will fetch http headers if a web page is running on the host. This could also be utilized in a sweep scan in an attempt to find vulnerable web servers.


### Windows Port Scanning (Living Off The Land)

Sometimes we only have a windows laptop and no internet access, to we must make use of what's available to us through the operating system.

`Test-NetConnection -Port <port> <target_IP>` - Power shell command that can be used to run a port scan from a windows machine. This command will check if the target responds too ICMP and whether a specified TCP port on the target is open. The result of `TcpTestSucceeded` indicated whether the port is open or not.

`1..1024 | % {echo ((New-Object Net.Sockets.TcpClient).connect("TARGET_IP", $_)) "TCP port $_ is open"} 2>$null` - This is a power shell one-liner that will scan the first 1024 ports of our target specified.

### RustScan

### MASSCAN