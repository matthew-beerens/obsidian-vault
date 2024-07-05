
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

