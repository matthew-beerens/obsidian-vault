
nmap by default scans the top 1000 ports using SYN scan when root or TCP as user.

# Responses

state responses from nmap:
- `open` - connection to the port was established (tcp, udp or sctp)
- `closed` - packet we received back from scan was RST (can also be used to check if host is alive)
- `filtered` - cannot correctly identify whether port is open (either no response or error code from target)
- `unfiltered` - result of TCP-ACK scan (port is accessible but non-determinant whether port is open or closed)
- `open|filtered` - non response for a specific port, indicates packet filter or firewall may protect port
- `closed|filtered` - only occurs during IP ID idle scans, indicates impossible to determine if closed or filtered by firewall

# Methods

## Port basics

`--top-ports=X` - used nmap db to scan the top X amount of ports
`-p 22,25,80,139,445 or -p 22-445` - example of port range
`-p-` - scan all ports
`-F` - fast scan (top 100 ports)

## Scan basics

### Basic clear scan

the below command can be used to get a clear view of our scan and exactly what is happening in relation to our scan packets:

```
sudo nmap $RHOST -p $PORT --packet-trace -Pn -n --disable-arp ping
```

`-Pn` disables ICMP echo requests
`-n` disables DNS resolution (this can speed things up by 20% when we don't need the extra dns data)

### Connect Scan

`-sT` uses the TCP three-way handshake and is the most accurate way to determine if a port is open.

It sends a `SYN` packet and waits for a response. The port is considered open if the port responds with a `SYN-ACK` packed and closed if responded with `RST` packet.

This is also the most stealthy method. It does not leave any unfinished connections or unsent packets on the target host. This means it is less likely to be detected by IDS or IPS.

Most useful for mapping the network and not causing any disruptions.

This type of scan is also useful when the target host has a personal firewall that drops incoming packets but allows outgoing packets. In this case a connect scan can bypass the firewall and accurately determine the port state.

This scan is also the slowest as it has to wait for the connection/response after each packet it sends.

connect scan example:

```
sudo nmap $RHOST -p $PORT --packet-trace --disable-arp-ping -Pn -n --reason -sT
```

this command runs a clear trace of the scan and a reason for the connection result.


# Filtered Ports

There can be several reasons for filtered ports. In most cases it is due to firewalls having certain rules setup to handle specific connections.

This can result in either a dropped or rejected packet.

`Dropped packets` mean nmap receives no response. By default nmap `--max-retries` is set to `1` to make sure the packet wasn't mishandled the first time.

`Rejected packets` will respond with an `ICMP Reply` with `type 3` and `error code 3`. This indicates the desired port is unreachable. If we know the host is alive we can strongly assume that a firewall on this port is rejecting the packets. We will have to look closer at this port later.

# Discovering Open UDP ports

System admins sometimes forget to filter UDP ports like TCP ones.

UDP being stateless and not requiring a three-way handshake, doesn't require acknowledgement, and only waits for the response making the timeout time much longer, and the scan longer.

This scan is much slower than TCP.

This scan can be performed using `-sU`

Example scan of top 100 UDP ports:

```
sudo nmap $RHOST -F -sU
```


We don't get a response from UDP scans as nmap send only empty datagrams, if the port is open, we only get a response if the application is configured to respond.


# Service Enumeration

It is recommended to do a quick port scan first to get a small overview of the available ports to generate less traffic to prevent detection and blocking.

We can then run service detection using `-sV` against the discovered ports.

Service detection works by `banner grabbing` and then if that is not enough uses a `signature-based matching system` (which significantly increases the scans duration)

Sometimes the automatic scan can miss information because nmap doesn't know how to handle it. This information can be revealed though `--packet-trace`.

## Further probing

We can further investigate a service and probe it for more information using `tcpdump` and `netcat/nc` to see what nmap didn't show us.

we can start a `tcpdump` to capture network packets:

```
sudo tcpdump -i eth0 host $LHOST and $RHOST
```

and then attempt a banner grab using `netcat`:

```
nc -nv $RHOST $RPORT
```

from the output the `PSH` and `ACK` flags states the target server is sending data to us and with ACK informs us all the data has been sent.

PSH flag may contain the extra information not present in nmap.

From here we can see the banner captured with netcat and also the packet exchange in the tcpdump output.

# NSE (Nmap Scripting Engine)

The NSE provides 14 categories of scripts that can be made of use for further enumeration:
- auth - confirmation of auth credentials
- broadcast - host discovery
- brute - bruteforce services with credentials
- default - default scrips executed by `-sC`
- discovery - eval of accessible services
- dos -  dossable services
- exploit - scripts tried to exploit known vulnerabilities
- external - scripts using external services for further processing
- fuzzer - uses scripts to identifiy vulns and unexpected packet handling
- intrusive - intrusive scripts that could negatively affect the target system
- malware - checks if some malware infects the target system
- defensive - scripts that do not perform intrusive and destructive access
-  version - extension for service detection
- vuln - identification of specific vulnerabilites

examples:

```
sudo nmap <target> -sC

sudo nmap <target> --script <category>

sudo nmap <target> --script <script-name>,<script-name>,...

sudo nmap 10.129.2.28 -p 25 --script banner,smtp-commands

sudo nmap 10.129.2.28 -p 80 -A

sudo nmap 10.129.2.28 -p 80 -sV --script vuln

```
# Additional

we can generate HTML output of the nmap scan by generating XML output with `-oX` and then generating the html with:

```
xsltproc target.xml -o target.html
```

`--stats-every=Xs` can be used to display scan stats and every X seconds intervals.