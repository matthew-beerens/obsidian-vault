
### Goals

- gain access to user account
- enumerate
- repeat for all extra access we gain

### Manual Enumeration

- start with users and groups
- then OS
- Permissions and loggon on users
- Service Principal names
- Object permissions
- Domain Shares

`CREATE A DOMAIN MAP WHILE ENUMERATING - VISUALIZING HELPS TO FIND ATTACK VECTORS`

#### Legacy Windows Tools (net.exe)
##### Users

We can enumerate all the users on a domain with:
```
net user /domain
```

User this list of users to further enumerate the users in the domain:
```
net user <user> /domain
```

From the output we may be able to find the users are in some interesting groups, like `Domain Admins`.

##### Groups

Enumerate groups that are on the domain:
```
net group /domain
```

With the output we can further enumerate the groups to determine what users belong to those groups:
```
net group "Group" /domain
```


#### PowerShell and .NET

PowerShell cmdlets are only installed by default on domain controllers as part of Remote Server Administration Tools (RSAT).

We can create our own tools and scripts by interacting with LDAP using Active Directory Services Interface (ADSI - a set of interfaces built on COM) as an LDAP provider.

AD enumeration relies on LDAP. It is used as the communication channel for AD queries.

To communicate with AD we need a specific LDAP ADsPath.
```
LDAP://HostName[:PortNumber][/DistinguishedName]
```

`hostname` - computer name, IP address or domain name. We should aim to use the `Primary Domain Controller (PDC) as it has the most current information`. To do that we need to find the DC holding the `PdcRoleOwner` property. We can do this with PowerShell and .NET.

`portnumber` - optional

`DistName` - uniquely identifies an object in AD, including the domain itself.

DN looks something like so (this is the Stephanie user object):
```
CN=Stephanie,CN=Users,DC=corp,DC=com
```

`CN` - common name - identifies an object in AD
`DC` - domain component - represents the top of an LDAP tree - we refer to this as the distinguished name of the domain itself.

The `DN` is read from right to left. Which you can see takes the natural form from top level domain down to the object/user level.

`For enumeration purposes we are interested in the DCs`.

##### Writing a Script

1. find the PDC - this is a PowerShell command
```
$DomainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = $DomainObj.PdcRoleOwner.Name
```

2. get the DN
```
$DN = ([adsi]'').distinguishedName
```

3. construct LDAP path:
```
$LDAP = "LDAP://$PDC/$DN"
```


example output of script:
```
.\enumeration.ps1
LDAP://DC1.corp.com/DC=corp,DC=com
```


##### Adding searching functionality 

To add search functionality we can use the  System.DirectoryServices namespace, using DirectorySearcher and DirectoryEntry classes.

`DirectoryEntry` encapsulates an object in the AD service hierarchy. We will provide it our LDAP path. We can also provide it credentials but there's no need if we are on an authenticated user.

`DirectorySearcher` performs queries against AD using LDAP. We must specify the AD service we want to query using the `SearchRoot` property. We will pass `DirectoryEntry` for this.

```
$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)

$result = $dirsearcher.FindAll()
```

`This will generate alot of output - listing everything in the domain`

We can filter this output to only list `computer, user, and group` objects.

Before `FindAll()` use:
```
$dirsearcher.filter="samAccountType=805306368"
```

This will list the objects that made it through the filter and a list of its properties.

To print out all the properties of an object we will need to iterate through it:
```
Foreach($obj in $result)
{
	Foreach($prop in $obj.Properties)
	{
		$prop
	}
	Write-Host "-----------------------------"
}
```

which will give us some output like this:

```
...
logoncount                     {173}
codepage                       {0}
objectcategory                 {CN=Person,CN=Schema,CN=Configuration,DC=corp,DC=com}
dscorepropagationdata          {9/3/2022 6:25:58 AM, 9/2/2022 11:26:49 PM, 1/1/1601 12:00:00 AM}
usnchanged                     {52775}
instancetype                   {4}
name                           {jeffadmin}
badpasswordtime                {133086594569025897}
pwdlastset                     {133066348088894042}
objectclass                    {top, person, organizationalPerson, user}
badpwdcount                    {0}
samaccounttype                 {805306368}
lastlogontimestamp             {133080434621989766}
usncreated                     {12821}
objectguid                     {14 171 173 158 0 247 44 76 161 53 112 209 139 172 33 163}
memberof                       {CN=Domain Admins,CN=Users,DC=corp,DC=com, CN=Administrators,CN=Builtin,DC=corp,DC=com}
whencreated                    {9/2/2022 11:26:48 PM}
adspath                        {LDAP://DC1.corp.com/CN=jeffadmin,CN=Users,DC=corp,DC=com}
useraccountcontrol             {66048}
cn                             {jeffadmin}
countrycode                    {0}
primarygroupid                 {513}
whenchanged                    {9/19/2022 6:44:22 AM}
lockouttime                    {0}
lastlogon                      {133088312288347545}
distinguishedname              {CN=jeffadmin,CN=Users,DC=corp,DC=com}
admincount                     {1}
samaccountname                 {jeffadmin}
objectsid                      {1 5 0 0 0 0 0 5 21 0 0 0 30 221 116 118 49 27 70 39 209 101 53 106 82 4 0 0}
lastlogoff                     {0}
accountexpires                 {9223372036854775807}
...
```

We can also filter by any property of any object type. For example by name:
```
$dirsearcher.filter="name=jeffadmin"
```

```
Foreach($obj in $result)
{
    Foreach($prop in $obj.Properties)
    {
        $prop.memberof
    }

    Write-Host "-------------------------------"
}
```

This would print our all the groups `jeffadmin` is a member of.

To enable our script to be more dynamic and accept parameters from the command line we can do something like this:
```
function LDAPSearch {
    param (
        [string]$LDAPQuery
    )

    $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
    $DistinguishedName = ([adsi]'').distinguishedName

    $DirectoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://$PDC/$DistinguishedName")

    $DirectorySearcher = New-Object System.DirectoryServices.DirectorySearcher($DirectoryEntry, $LDAPQuery)

    return $DirectorySearcher.FindAll()

}
```

which we could then run like this:
```
Import-Module .\function.ps1

LDAPSearch -LDAPQuery "(samAccountType=805306368)"

LDAPSearch -LDAPQuery "(objectclass=group)"
```

We could also enumerate the objects from the command-line like this:
```
foreach ($group in $(LDAPSearch -LDAPQuery "(objectCategory=group)")) {
>> $group.properties | select {$_.cn}, {$_.member}
>> }
```

or like this:
```
$sales = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Sales Department))"

$sales.properties.member
```

#### What to look our for

Groups can be members of groups (Nested groups). This means members in encapsulated groups are also members of the outer groups - leading to misconfigurations and potentially more privileges than expected.


### PowerView Enumeration

`/usr/share/windows-resources/powersploit/Recon/PowerView.ps1`

PowerView is a PowerShell script for interacting with and enumerating AD.

#### Groups and Users


To start using PowerView:
```
Import-Module .\PowerView.ps1
```

To get domain information:
```
Get-NetDomain
```

Get all users in the domain:
```
Get-NetUser
```

This puts out a lot of information - every property of users.

We can filter this for information we want by piping to `Select`:
```
Get-NetUser | select cn
```

This will print all the users in the domain.

To enumerate users password habits or if they have a week password because of policy changes we could enumerate like so:
```
Get-NetUser | select cn,pwdlastset,lastlogon
```

This may reveal a user hasn't logged in for a long time and may have a weaker password - or maybe not at all and has a weak default password set.

To enumerate groups:
```
Get-NetGroup | select cn
```

To enumerate members of a group:
```
Get-NetGroup | select member
```

To enumerate groups of a group:
```
Get-NetGroup | select group
```


#### Operating System

Enumerate computer objects in the domain:
```
Get-NetComputer | select operatingsystem,dnshostname
```

This command can be used to gain a lot of information, but the above filtering will be easier to view what computers are on the system and their hostname which may reveal their role within the domain.


#### Permissions and Logged on Users

Enumerate if we have any administrative access to machines on the domain:
```
Find-LocalAdminAccess
```

This command relies on the `OpenServiceW` function. It connects to the `Service Control Manager `(SCM) on target machines. SCM maintains a database of installed services and drives on Windows Computers. This command attempts to open this database with the `SC_MANAGER_ALL_ACCESS` right (which requires admin privs). If the connection is successful it is assumed we have administrative access to that machine. 

Enumerate current logged in users on a machine:
```
Get-NetSession -ComputerName <hostname> -Verbose
```

This command makes use of NetWkstaUserEnum and NetSessionEnum functions form the winapi.
They're the most reliable but may not work a lot of the time, as one of these requires admin privs and the other doesn't. In recent Windows Versions it also wont be able to access the necessary registry hive to gather this information. `So this is mainly useful for older versions of windows`. It relies on the `SrvsvcSessionInfo` located `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity`

We can instead use PSLoggedon.exe:
```
.\PsLoggedOn.exe \\hostname
```

This enumerates registry keys under HKEY_USERS to retrieve SIDs and map them to usernames, It also uses the NetSessionEnum winapi function, but relies on the `Remote Registry` service.

`If we find we have administrative access to a computer - and through the above commands reveal there is another user logged in on the system - we may be able to steal their crednetials as their credentials would be cached on the system`


#### Enumerating Service Principal Names (Service Accounts)

Service accounts while may not have Admin Privs, may have higher privs than usual or are members of higher prived groups.

Services launched by the system itself run in the context of a `Service Account`:
- LocalSystem
- LocalService
- NetworkService

More complex applications may use a domain user for the needed context while still maintaining access to resources inside the domain.

When applications MSSQL are integrated into AD, a unique service instance known as a `Service Principal Name (SPN)` associates a service to a specific service account in AD.

There is also the introduction of `Managed Service Accounts` the handle this. Which on or the other may be in use depending on the server/windows version.

Enumerate SPNs in the domain:
```
setspn -L iis_service
```

This command will search for all clients/servers in the domain for the presence of the iis_service (which is a user we found) SPN.

We can also enumerate for an SPN is to use:
```
Get-NetUser -SPN | select samaccountname,serviceprincipalname
```

This command will enumerate all users on the domain to obtain a clear list of SPNs.

`if we find a location like a webdomain to checkout in the results we can use nslookup to find the ip`

```
nslookup.exe <domain>
```


#### Enumerating Object Permissions

An AD object may have a set of permissions applied to it with multiple `Access Control Entries (ACE)`. These ACEs make up the ACL. Each ACE defines whether access to the specific object is allowed.

When a user tries to access and object in AD a check will be made against the ACL to see if a there's an ACE that allows the user to access the object. To do this, as part of the process of accessing an object he user must send an `access token` which contains the users identity and permissions.

The most interesting permissions we're interested in include:
```
GenericAll: Full permissions on object
GenericWrite: Edit certain attributes on the object
WriteOwner: Change ownership of the object
WriteDACL: Edit ACE's applied to object
AllExtendedRights: Change password, reset password, etc.
ForceChangePassword: Password change for object
Self (Self-Membership): Add ourselves to for example a group
```

To enumerate ACEs of a user with `PowerView.ps1` we can use:
```
Get-ObjectAcl -Identity stephanie
```

This will print our every single ACE applied to the Stephanie user. This is how we do it for every object (group, user, etc)

The properties we are mainly interested from this output include:
- ObjectSID
- ActiveDirectoryRights
- SecurityIdentifier

To decipher SIDs we gather from this output we can use powerview:
```
Convert-SidToName <SID>
```

To further enumerate all objects in we could do something like:
```
Get-ObjectAcl -Identity "Management Department" | ? {$_.ActiveDirectoryRights -eq "GenericAll"} | select SecurityIdentifier,ActiveDirectoryRights
```

This command would print all the ACEs that have the `GenericAll` privilege which is the highest.

We could then convert all the SIDs to names we find like so:
```
"S-1-5-21-1987370270-658905905-1781884369-512","S-1-5-21-1987370270-658905905-1781884369-1104","S-1-5-32-548","S-1-5-18","S-1-5-21-1987370270-658905905-1781884369-519" | Convert-SidToName
```

If we found we had permissions to a powerful group and we can our selves, we could use this:
```
net group "Management Department" stephanie /add /domain
```


#### Enumerating Domain Shares

Domain shared often contain critical information about the environment.

We can list all domain shares using powerview:
```
Find-DomainShare
```

This will show us shares available to us:
```
Find-DomainShare -CheckShareAccess
```

`We should search each share we come across in search for valuable information.`

`SYSVOL` is a particularly valuable share to check as it may include folders and files that reside on the domain controller itself. It is typically used for various domain policies and scripts. 
By default, the **SYSVOL** folder is mapped to `**%SystemRoot%\SYSVOL\Sysvol\domain-name**` on the domain controller and every domain user has access to it.

```
ls \\dc1.corp.com\sysvol\corp.com\
```

```
cat \\dc1.corp.com\sysvol\corp.com\Policies\oldpolicy\old-policy-backup.xml
```

If we find an encrypted password we may be able to decrypt it with `gpp-decrypt`:

```
PS C:\Tools> cat \\dc1.corp.com\sysvol\corp.com\Policies\oldpolicy\old-policy-backup.xml
<?xml version="1.0" encoding="utf-8"?>
<Groups   clsid="{3125E937-EB16-4b4c-9934-544FC6D24D26}">
  <User   clsid="{DF5F1855-51E5-4d24-8B1A-D9BDE98BA1D1}"
          name="Administrator (built-in)"
          image="2"
          changed="2012-05-03 11:45:20"
          uid="{253F4D90-150A-4EFB-BCC8-6E894A9105F7}">
    <Properties
          action="U"
          newName=""
          fullName="admin"
          description="Change local admin"
          cpassword="+bsY0V3d4/KgX3VJdO/vyepPfAN1zMFTiQDApgR92JE"
          changeLogon="0"
          noChange="0"
          neverExpires="0"
          acctDisabled="0"
          userName="Administrator (built-in)"
          expires="2016-02-10" />
  </User>
</Groups>
```

```
gpp-decrypt "+bsY0V3d4/KgX3VJdO/vyepPfAN1zMFTiQDApgR92JE"
```

