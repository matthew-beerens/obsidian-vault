
# Footprinting

```
sudo nmap -sV -p21 -sC -A $RHOST
```
# Basics
FTP runs within the application layer of the TCP/IP stack. These protocols work with the support of browsers or email clients to perform their services. There are also special FTP programs for ftp.

In an FTP connection two channels are opened. 
1.  the client and server establish a control channel through tcp port 21.
2. client sends commands to the server
3. server returns status codes
4. then both server and client can establish the data channel on tcp port 20 (used exclusively for data transmission).
5. the protocol then watches for errors during this process
6. if connection is broken it can be resumed afterwards

There is `active` and `passive` FTP.

In `active ftp`, the client establishes the connection via port 21 and informs the server via which client side port the server can transmit its responses. However, if a firewall protects the client, the server cannot reply because external connections are blocked.

This is where `passive ftp` comes in. The server announces a port that the client can establish the data channel. Since the client initiates the connect the firewall does not block the transfer.

# TFTP

Trivial file transfer protocol is a simpler FTP without authentication. It also runs using udp, so is unreliable. It solely relies on read write permissions of files within directories to control who can read and write files within a TFTP server.

some TFTP commands:
- connect
- get
- put
- quit
- status
- verbose

# vsFTPd

`vsFTPd` is one of the most popular Linux ftp services. The default configuration can be found at `/etc/vsftpd.conf` which some are predefined by default.

We can also view which users are `not allowed` to log in/user ftp via `/etc/ftpusers`

# Dangerous Settings

There are many different security related settings we can make on each ftp server.
These may be used for various reasons including testing connections through firewalls, routes, and authentication.

One import authentication setting is the `anonymous user`. Often used to allow everyone on the internal network to share files and data without accessing each others computers. 

anonymous user enabled settings:
```
anonymous_enable=YES	    Allowing anonymous login?
anon_upload_enable=YES	    Allowing anonymous to upload files?
anon_mkdir_write_enable=YES	Allowing anonymous to create new directories?
no_anon_password=YES	    Do not ask anonymous for password?
anon_root=/home/username/ftp   Directory for anonymous.
write_enable=YES	        Allow the usage of FTP commands: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE, and SITE?
dirmessage_enable=YES	    Show a message when they first enter a new directory?
chown_uploads=YES	        Change ownership of anonymously uploaded files?
chown_username=username	    User who is given ownership of anonymously uploaded files.
local_enable=YES	        Enable local users to login?
chroot_local_user=YES	    Place local users into their home directory?
chroot_list_enable=YES	    Use a list of local users that will be placed in their home directory?
hide_ids=YES	            All user and group information in directory listings will be displayed as "ftp".
ls_recurse_enable=YES	    Allows the use of recurse listings.
```

Allowing anonymous users is one of the most common ftp server configurations.

# FTP actions

We can connect to ftp with:
```
ftp $RHOST

ftp anonymous@$RHOST
```

We will receive `status code 220` and the `service banner`.

we can use `ls` to list the directory's content.

To gain a further overview of the FTP servers settings we can utilize `status` command. 

To further investigate we can also make use of `debug` and `trace`. This will enable more verbose output and allow us to see the packet tracing, including response codes and descriptive messages.

To list the whole of the directory tree we can utilize:
```
ls -R
```
 If it is enabled within the ftp conf.

Once we find we have access to an ftp server and if we wish to, we can download the whole directory using:
```
wget -m --no-passive ftp://anonymous:anonymous@$RHOST
```

We can check if we have upload permissions - often files are synchronized with a webserver as it gives developer quick access to files and making changes for webservers. Most of the time config errors are found. This can lead us to gain a reverse shell with upload privileges:
```
touch testupload.txt
ftp $RHOST
put testupload.txt
```

With a status 200 we realize the potential for planting a reverse shell/webshell.

We can interact with an ftp server using `nc, telnet and openssl`:
```
nc -nv $RHOST 21
```

```
telnet $RHOST 21
```

If ftp server is making use of ssl/tls we need a client that can handle it:
```
openssl s_client -connect $RHOST:21 -starttls ftp
```

This will also show us the ssl certificate of the server which may also be useful.

