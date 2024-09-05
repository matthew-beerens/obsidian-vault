
create a directory and break it up into a file structure that reflects our initial public machines we are going to enumerate

# Initial Enumeration

start off with nmap scan:
```
sudo nmap -sC -sV -oN mailsrv1/nmap 192.168.50.242
```

This will fingerprint the machine and give us an idea of what the machine is used for.

We should research technologies we find and google to see if there is any vulnerabilities attached. CVE Details is helpful for this.

After the initial scan we should further enumerate the services running on the machine. This includes webservers and smb.

If we find nothing after enum of the machine we may be able to come back to it later with information or credentials we find during enumeration or exploitation of another machine.

