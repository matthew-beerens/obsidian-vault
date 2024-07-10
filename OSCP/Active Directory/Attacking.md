
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

