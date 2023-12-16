
SMB protocol runs on port 445 along with NetBIOS protocol on port 139 and many UDP ports. NetBIOS is an independent session layer protocol and service that allows computers on a local network to communication with each other. SMB can work without NetBIOS, NetBIOS over tcp is require for backward compatibility. Enumeration of the pair go hand-in-hand.

### Scanning SMB

`nmap -v -p 139,445 -oG smb.txt 192.168.1.1-254` - will scan all machines within the network for the presence of NetBIOS and SMB.

`nmap -v -p 139,445 --script smb-os-discovery <ip>` - enumerate OS using smb NSE script. NSE scripts often provide more information. This type of script will reveal the domain name and AD domain services. This approach will also produce less traffic.

`enum4linux <ip>` - emumerate smb shares automaticallys

`for ip in (cat ips.txt); do enum4linux $ip; done ` - bash one-liner for enumerating a lists IPs with smb service running.

### Scanning NetBIOS

`sudo nbtscan -r 192.168.1.0/24` - Queries the NetBIOS name service for valid NetBIOS names. `-r` specifies the originating UDP port as 137. This information can be used to further understand the context of the scanned hosts. NetBIOS names are often descriptive about the role of the host.


### Windows

`net view \\<hostname> /all` - This command will list domains, resources and computers belonging to a given host. Shares ending with `$` are administrative shares.

