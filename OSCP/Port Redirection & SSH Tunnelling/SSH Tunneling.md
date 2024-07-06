`openssh version must be greater than 7.6 for dynamic port forwarding`

when scanning through tunnel make sure to use `-n` within nmaps flags

When getting a foothold always ensure we have a tty using either python bin bash spawning or otherwise.

### Local Port Forwarding

SSH tunneling gives us end to end communication with a remote host that may only be accessible through another host we have compromised.

SSH will allow us to send almost any kind of data over the tunnel which will allow us to use our kali machine locally to interact with the remote target.

Once we have a foothold on the pivot machine we need to determine the next hop and setup our tunnel.

```
ssh -N -L 0.0.0.0:<port>:<target_ip>:<target_port> user@pivot_machine
```

above you can see we are starting a listening port on the foothold machine for our kali machine with 0.0.0.0, then we are selecting which ip address and port we want to forward to, and then connecting to the machine that is responsible for passing the the information to our target.

0.0.0.0 - is our foothold (ssh client)
pivot machine (ssh server) - the target directly connected to our foothold
target_ip (target forwarded to)- machine only reachable through our pivot machine.

We can confirm this connection set up correctly by getting another foothold on another port for the foothold machine and check connections with `ss -ntplu`

Once we know it is setup we can run any tools that will allow use to configure port and have them run against the target machine

`smbclient -p 4455 -L //192.168.234.63/ -U hr_admin --password=Welcome1234` - example of enumerating smb 2 hops away

`everything we do must be against the original foothold host and against the listening port - everything wil be forwarded to the specific port we selected for forwarding.`


### Dynamic Port Forwarding

Setting up dynamic port forwarding is similar to local port forwarding, instead we don't specify a forwarding interface, we specify its dynamic, and only specify the listening port

```
ssh -N -D 0.0.0.0:<listen_port> user@ssh_server
```

`The limitiation is that we have to ensure the software we use is SOCKS compliant as it uses a socks proxy to achieve this flexible functionality - and some software is not compatiable with socks format` - we fix this issue by using proxychains

Proxychains can be configure with `/etc/proxychains4.conf`

replace the proxy definition at the bottom of the file with `socks5 <foothold_ip <listen_port>`

then we can utilize proxychains to communicate over our dynamic port forward
```
proxychains smbclient -L //ip/ -U <user> --password=<password>
```

`this wont work with everything but it will work in a lot of cases`


By default, Proxychains is configured with very high time-out values. This can make port scanning really slow. Lowering the tcp_read_time_out and tcp_connect_time_out values in the Proxychains configuration file will force Proxychains to time-out on non-responsive connections more quickly. This can dramatically speed up port-scanning times.


### Remote Port Forwarding

before we can conduct remote port forward, we need to ensure we have a strong ssh key set up

and we need to start our ssh server:
```
sudo systemctl start ssh
```

then once we have a remote shell on the target machine:
```
ssh -N -R 127.0.0.1:<port>:<target_ip_forward>:<port> kali@kali_ip
```

127.0.0.1 is the ssh server - the kali loopback
target_ip - the target were trying to forward our packets to
kali_ip is our ip

we can then ssh to the kali listening ip and port
```
ssh user@127.0.0.1 -p port
```

### Remote Dynamic Port Forwarding

Remote dynamic port forwarding has only been available since October 2017's OpenSSH 7.6.[2](https://portal.offsec.com/courses/pen-200-44065/learning/port-redirection-and-ssh-tunneling-48849/ssh-tunneling-48917/ssh-remote-dynamic-port-forwarding-48854#fn-local_id_715-2) Despite this, only the OpenSSH _client_ needs to be version 7.6 or above to use it - the server version doesn't matter.

```
ssh -N -R <port> kali@kali_ip
```

as you can see for this technique we only need to supply the listening socket.

we will then need to update our /etc/proxychains4.conf
```
socks5 127.0.0.1 <port>
```

when can then run any command against any interface remote with `proxychains`

`in this case kali is still the server and the target is the client - we need to get a foothold and run our ssh command on the client (target)`

`sudo ss -ntplu` - confirm connection

### Using sshuttle

requires root privs on ssh client and python3 on the ssh server

we set up a port forward listener on our target and forward it to a destination with a ssh port running.

```
socat TCP-LISTEN:2222,fork TCP:10.4.50.22
```

on our kali machine we can then use sshuttle to create routes to subnets we have discovered on the target:

```
sshuttle -r user@target:2222 10.4.50.0/24 172.16.50.0/24
```

This is theory will allow use to interact with these subnets from our kali machine. This works kind of like a vpn.




