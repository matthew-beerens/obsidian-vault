
# Determine Firewall Rules

When a port is shown as filtered it can have several reasons, but generally occur when a packet is dropped due to firewall rules (no response).

Packets can be dropped out rejected.

rejected packets will send us a response with an ICMP error:
- Net Unreachable
- Net Prohibited
- Host Unreachable
- Host Prohibited
- Port Unreachable
- Proto Unreachable

## TCP ACK Scan

nmaps tcp ack scan is harder for firewalls and IDS/IPS. This is because they only send a tcp packet with only the ACK flag.

When a port os closed or open the host must respond with an RST flag.

These packets are often passed by firewalls because a firewall cannot determine whether the connection was first established from the external or internal network.

No response back will indicated that packets will be dropped.


# Detect IDS/IPS

It is much harder to detect IDS/IPS systems because they are passive monitoring systems.

With the IDS triggering security procedures within the IPS, consequently causing blocking of us from the network and potentially the internet from our IP, it is recommended to make use of virtual private servers (VPS) to prompt a blocking of our VPS to probe the IDS/IPS rules and mechanisms.

This process gives a knowledge of such systems and signals to be quieter with our scans and actions on the network.

# Decoys

Admins may block specific subnets from different regions. Preventing any access to the target network.

Another example is when IPS should block us. Which for this case the best choice is using a decoy scan.

we can conduct a decoy scan  using `-D` coupled with `RND:<num>` to specify how many random ip addresses should be used to attempt to shroud our IP.

example usage:

```
sudo nmap $RHOST -p $RPORT -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5
```

This will place our ip randomly against the random IPs.

For this approach the random IP addresses should be alive to prevent SYN flooding (dosing)

Spoofed packets are often filtered out by ISPs and routers, but we can also specify our VPS servers IP addresses and use them in combination with IP ID manipulation in the IP headers.

In some scenarios only individual subnets would not have access to the servers specific services, so we can also manually spoof the IP address with `-S` to test for better results.

example of an OS scan spoofing the source IP and directing traffic out of tun0:

```
sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0
```


# DNS Proxying

Generally DNS queries make it through firewalls because webservices are meant to be found and visited.

nmap gives us a way to specify dns servers to use with `--dns-server <ns>,<ns>`
If we find ourselves in a DMZ, company dns servers are usually more trusted than those from the internet. SO we may be able to use them to interact with hosts on the internal network.

We may be also to make user of `--source-port` to set the port to `53`, which may bypass firewall and IDS/IPS if not configured properly.

example:

```
sudo nmap $RHOST -p $RPORT -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53
```

We should try both UDP and TCP port 53, as 53 is increasingly becoming more used for DNS queries, and is already in use for zone transfers or sizes over 512.

If we find this strategy to work, IDS/IPS filter may be configured weaker than expected and we can test this by attempting nmap connections to found open ports:

```
ncat -nv --source-port 53 10.129.2.28 50000
```




`a mixture of all these techniques may need to be used`