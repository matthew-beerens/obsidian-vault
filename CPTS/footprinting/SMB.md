
# Footprinting

basic scanning Footprinting (not always full of info, try `rpcclient`):
```
sudo nmap 10.129.14.128 -sV -sC -p139,445
```

We can use `rpcclient` to further prod for more information:
```
rpcclient -U "" $RHOST
```

We can user the man page for helpful commands to use with rpcclient - below is a lost of common ones we may use for enumeration:

| **Query**                 | **Description**                                                    |
| ------------------------- | ------------------------------------------------------------------ |
| `srvinfo`                 | Server information.                                                |
| `enumdomains`             | Enumerate all domains that are deployed in the network.            |
| `querydominfo`            | Provides domain, server, and user information of deployed domains. |
| `netshareenumall`         | Enumerates all available shares.                                   |
| `netsharegetinfo <share>` | Provides information about a specific share.                       |
| `enumdomusers`            | Enumerates all domain users.                                       |
| `queryuser <RID>`         | Provides information about a specific user. e.g RID 0xXXX          |
| `querygroup <RID>`        | e.g RID 0xXXX                                                      |
|                           |                                                                    |

When we might not have access to all commands we may be able to use bash scripts or python to get the needed information, for example, enumdomusers (impackets samrdump can achieve this):
```
for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done
```

We can make use of `smbmap` to enumerate:
```
smbmap -H 10.129.14.128
```

as well as the use of `crackmapexec`:
```
crackmapexec smb 10.129.14.128 --shares -u '' -p ''
```

also` enum4linux-ng` installed from git (git clone https://github.com/cddmp/enum4linux-ng.git):
```
./enum4linux-ng.py 10.129.14.128 -A
```

list shares with a null session (no user creds):
```
smbclient -N -L //$RHOST
```

connect to a share:
```
smbclient //$RHOST/<share>
```

download object:
```
get <somthing.txt>
```

execute command on smb without interrupting connection:
```
!<cmd>

e.g
!ls

!cat <something.txt>
```

We can make use of this command to check which connections in detail there are to the smb:
```
smbstatus
```

# Basics

Server Message Block (SMB) is a client-server protocol that regulates access to files and entire directories and other network resources (printers, routers, interfaces) released for the network.

Information exchange between different system processes can also be handled based on the SMB protocol.

SMB first became available as part of the OS/2 network operating system LAN Manager and LAN Server.

SMB now serves as a way for all windows versions to communicate with one another over the network in a downward compatible manner.

Samba is also available for Linux distributions to support the communication via SMB between windows and Linux.

The SMB protocol enables the client to communicate with other participants in the same network to access files or services shared with it on the network

SMB is a TCP protocol and runs in a client/server architecture, so an established connection is required first.

Access right are then defined by ACLs. which can be configured for fine grained control like read, write, execute and full access for users or user groups. These rights are defined based on the shares, and are therefore independent of right defined on the local system.

In a network each host participates in the same workgroup (arbitrary collection of computers and their resources on an SMB network). Which there can be multiple.

NetBIOS (Network Basic Input/Output system) is an API for networking computers which supports this functionality. Machines need to undergo the `name registration` procedure when it goes online to participate in a NetBIOS environment. Each host reserves its hostname or the NetBIOS Name Server (NBNS) is used or Windows Internet Name Service (WINS).

# Samba

Samba is an alternative variant of SMB for unix based operating systems. It implements the `CIFS` network protocol (a dialect of SMB)

Usually connects of ports `137, 138, 139` when communication with smb enabled devices, but CIFS uses `445` only.

With SMBv3 Samba gained the ability to be a full member of an AD domain and with version 4 provides an AD controller. this is supported by `smbd` and `nmbd` daemons. which the SMB service controls. 

# SMB versions

| **SMB Version** | **Supported**                       | **Features**                                                           |
| --------------- | ----------------------------------- | ---------------------------------------------------------------------- |
| CIFS            | Windows NT 4.0                      | Communication via NetBIOS interface                                    |
| SMB 1.0         | Windows 2000                        | Direct connection via TCP                                              |
| SMB 2.0         | Windows Vista, Windows Server 2008  | Performance upgrades, improved message signing, caching feature        |
| SMB 2.1         | Windows 7, Windows Server 2008 R2   | Locking mechanisms                                                     |
| SMB 3.0         | Windows 8, Windows Server 2012      | Multichannel connections, end-to-end encryption, remote storage access |
| SMB 3.0.2       | Windows 8.1, Windows Server 2012 R2 |                                                                        |
| SMB 3.1.1       | Windows 10, Windows Server 2016     | Integrity checking, AES-128 encryption                                 |

# Default Configuration

We can check out the default config by installing samba and having a look at its config file:
```
cat /etc/samba/smb.conf | grep -v "#\|\;"
```


# Dangerous Settings

|**Setting**|**Description**|
|---|---|
|`browseable = yes`|Allow listing available shares in the current share?|
|`read only = no`|Forbid the creation and modification of files?|
|`writable = yes`|Allow users to create and modify files?|
|`guest ok = yes`|Allow connecting to the service without using a password?|
|`enable privileges = yes`|Honor privileges assigned to specific SID?|
|`create mask = 0777`|What permissions must be assigned to the newly created files?|
|`directory mask = 0777`|What permissions must be assigned to the newly created directories?|
|`logon script = script.sh`|What script needs to be executed on the user's login?|
|`magic script = script.sh`|Which script should be executed when the script gets closed?|
|`magic output = script.out`|Where the output of the magic script needs to be stored?|
Once an anonymous user can access a network service, it only takes one mistake to give them too many permissions or visibility and put the network at significant risk.

Lots of information can be leaked to anonymous users and may lead to the discovery of sensitive information such and new users and credentials to exploit.

