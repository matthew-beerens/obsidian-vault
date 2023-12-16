
Active directory works under a windows domain. 
Active Directory is the single repository inside the domain. 
there is a domain controller that runs the Active Directory services.

AD Basics
=
### windows domain

is a group of users and computers under the administration of a given business.

the idea behind a domain is to centralize the administration of common components of a windows network in a single repository call Active Directory

**Active Directory Domain Service** is the core of the windows domain, it holds a catalogue that holds information for all the objects that exist on your network, including:

#### Users 
one of the most common object types, object known as "security principals", meaning they can be authenticated and assigned privilege's over resources. 

used to represent two types of entities:
- people
- services

#### Machines
for every computer that joins an active directory domain, a machine object will be created, they are also considered security principals. this account type has limited rights within the domain.

Machine accounts are local administrators on the assigned computer and are generally not supposed to be accessed by anyone except the computer itself. but if you have the password you can use it to login.

machine passwords are automatically rotated out and generally made up of 120 random characters.

machines are identified by the machine name followed by a $.


#### Security Groups

like your generic user groups on windows, used to assign and manage privilege's to users. they are also considered security principals.

can have both machines and users

default groups (most important):
- domain admins - users with admin privs over entire domain, can administer any comp on the domain including DCs
- server operators - administer domain controllers, cant change any admin group memberships
- backup operators - users can access any file, ignoring their permissions
- account operators - users can create or modify other accounts
- domain users - all existing accounts in the domain
- domain computers - all existing computer in the domain
- domain controllers - all existing DCs in the domain

*security principals are objects that can act upon resources*

### Organizational Units

default organization units:
- Builtin - contains default groups available to any windows host
- Computers - any machine joining the network will be put here by default (except for DCs)
- Domain controllers - default OU that contains the DCs in your network
- users - default users and groups that apply to a domain-wide context
- managed service accounts - holds accounts used by services in your windows domain

### Security groups vs OUs

OUs - used for applying policies with specific configs that pertain to sets of users depending on their role in the enterprise.

Security Groups - grant permissions over resources

Powershell AD commands
=
`Set-ADAccountPassword username -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose` - reset the password of a user in AD

`Set-ADUser -ChangePasswordAtLogon $true -Identity username -Verbose` - force password change at next logon

`gpudate /force` - force update GPOs to the current computer on the AD


Segregating Computers
=
It is best to segregate computer into a least 3 organisational units:
- Workstations - most common devices, eash user in a domain will likely be logging into a workstation for normal work and browsing activities, these devices should never have a priv user signed into them.
- Servers - second most common device in AD domain. used to provide services to users or other servers
- domain controllers - third most common.allows you to manage the AD domain. these are the most sensitive devices within the network, they contain hashed passwords for al lusers within the environment.

### Group Policy Objects (GPO)

a collection of settings that can be applied to OUs, can contain policies aimed at users or computers, allowing you to set a baseline on specific machines and identities.

configured through "Group Policy Management". create a gpo under the Group Policy Objects directory, and link them to OUs. policies will be applied to OUs by placing them in the OU dir, it will be applied to the whole directory including children

### Group distribution

GPOs are distributed to the network via a network share called SYSVOL. which is stored in the DC.

Authentication Methods
=
services ask the domain controllers if the credentials entered are correct

two procotols used for network authentication in windows domains:
1. Kerberos - use by recent windows versions, default protocol in any recent domain
2. NetNTLM - legacy authentication protocol kept for compatibility

### Kerberos Authentication

users who sign in will be assigned tickets (proof of previous authentication). which can be presented to a service to demonstrate they have already been authenticated. which enables them to use services.

process:
1. user sends username and timestamp encrypted using a key derived from their password to the key dirstribution center (KDC, installed on the DC in charge of creating kerberos tickets)
2. KDC will create and send back a ticket granting ticker (TGT) which allows uers to request additional tickets to access specific services along with a sessions key which is needed to generate the requests
3. a user will use their TGT or ask the KDC for  ticket granting service (TGS) with the TGT. TGS allows connection to a specific service they were created for.
	1. to get a tgs a user witll send their usname, encrypted timestamp and the TGT along with a service principal name (SPN, inidcated specific service we intend to access)
4. TGS is encrypted with a key derived from the Service Owner hash. the service owner is the user or machine account the the service run under
5. TGS can then be sent to the desired serivce to authenticate


### NetNTLM Authentication

1. The client sends an authentication request to the server they want to access.
2. The server generates a random number and sends it as a challenge to the client.
3. The client combines their NTLM password hash with the challenge (and other known data) to generate a response to the challenge and sends it back to the server for verification.
4. The server forwards the challenge and the response to the Domain Controller for verification.
5. The domain controller uses the challenge to recalculate the response and compares it to the original response sent by the client. If they both match, the client is authenticated; otherwise, access is denied. The authentication result is sent back to the server.
6. The server forwards the authentication result to the client.

Note that the user's password (or hash) is never transmitted through the network for security.

**Note:** The described process applies when using a domain account. If a local account is used, the server can verify the response to the challenge itself without requiring interaction with the domain controller since it has the password hash stored locally on its SAM.


Trees
=
partitioning a domain into multiple domains is called a tree, comprised of a root domain and root DC and subdomains a sub DCs

this is useful for different requirements and regulations, like if we have expansions to different countries, it easier to manage and maintain

sub domains cannot control anything outside of their sub domain, enterprise admins need to be introduced

### Enterprise admins

can control all subdomains, required when treeing a domain

Forrests
=
the unification of separate trees with different namespaces is called a forrest
common when companies aquire other companies, the trees are joined to create a forrest


Trust Relationships
=
sometimes users need access to files in other trees or domains than their own so trust relationships are used to achieve this

### one-way trust domains
AAA domain trust BBB domain so BBB domain can access AAA domain resoures
AAA domain cant access BBB domain resources

### two-way trust domains
Two domains trust each other and can access each others resources
By default, joining several domains under a tree or a forest will form a two-way trust relationship.

trust relationship between domains doesn't automatically grant access to all resources on other domains. Once a trust relationship is established, you have the chance to authorise users across different domains, but it's up to you what is actually authorised or not