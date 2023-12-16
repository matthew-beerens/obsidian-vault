
USE TELNET FOR ENUMERATION
USE `sendEmail` to send mail.

https://book.hacktricks.xyz/network-services-pentesting/pentesting-smtp

https://mailtrap.io/blog/smtp-commands-and-responses/

SMTP servers if vulnerable can be used to verify emails and see the membership of a mailing list.

`nc -nv <ip> 25` - we connect to an SMTP server using Netcat.

`VRFY root` - test if we can use VRFY to query the server.

`VRFY idontexit` - see what a rejection message looks like (user doesn't exist)

`These steps can be used to automate the guessing of valid usernames.`

Below is a python script for automating the checking of valid usernames of an SMTP server:

````
#!/usr/bin/python

import socket
import sys

if len(sys.argv) != 3:
        print("Usage: vrfy.py <username> <target_ip>")
        sys.exit(0)

# Create a Socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Connect to the Server
ip = sys.argv[2]
connect = s.connect((ip,25))

# Receive the banner
banner = s.recv(1024)

print(banner)

# VRFY a user
user = (sys.argv[1]).encode()
s.send(b'VRFY ' + user + b'\r\n')
result = s.recv(1024)

print(result)

# Close the socket
s.close()
````


### Windows


`Test-NetConnection -Port 25 <ip>` - Test if an SMTP server is up and running. We cannot interact with SMTP using Test-NetConnection.

`dism /online /Enable-Feature /FeatureName:TelnetClient` -  Install and enable the telnet client for windows if it is not already so. We can use this to interact with SMTP. Installing Telnet will require administrative privs.

`we can also transfer an telnet binary from c:\windows\system32\telnet.exe from another dev machine of ous to the machine we are testing on`

`telnet ip 25` - connect to SMTP server.

`after this enumeration can be conducted like above using VRFY and EXPN`


#### Telnet

use telnet to interact with smtp servers if we get no response using netcat.

`EHLO <domain/ip smtp client>` - see what function we have available
`AUTH LOGIN` - authentication based login - will need to provide base64 encoded values for username and password


#### Sending mail

use `sendEmail` command on kali linux.