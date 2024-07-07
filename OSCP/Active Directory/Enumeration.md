
### Goals

- gain access to user account
- enumerate
- repeat for all extra access we gain

### Manual Enumeration

- start with users and groups


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

