
#### Key Information

we need to obtain information about the system the we have gained access to in order to attempt privilege escalation.

```
- Username and hostname
- Group memberships of the current user
- Existing users and groups
- Operating system, version and architecture
- Network information
- Installed applications
- Running processes
```

###### User and group information

`whoami` to gain username and hostname

`whoami /groups` gain information about groups our current user is a member of

`net user` & `net group` gain information about other users and groups on the system

`Get-LocalUser` & `Get-LocalGroup` gain information about other users and groups on the system using powershell

`Get-LocalGroupMember <group_name>` gain information about users that are members of the `group_name` using powershell

###### System and network information

`systeminfo` gain information about system information, including os, version, system type, etc. we can use the build number to get an exact version match of the operating system.

`ipconfig /all` gain information about the network and possible other interfaces and networks that may be connected to the target we could shift to. other network relevant network information.

`route print` gain information about other systems or networks we can connect to, prints all routes of the system we are currently on.

`netstat -ano` display all active connections on the system. `-a` is all connections (TCP and UDP ports), `-n` disable name resolution, `-o` show the process id for each connection.

`Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname` display all 32 bit installed applications on the system

`Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname` display all 64 bit installed applications on the system

*we should always check the 32bit and 64bit program files directories and downloads due to flawed installation processes*

`Get-Process` identify running programs and processes on the system

`Get-Process -Id 8444 | Select-Object -Property ProcessName, Id, WS, Path` - get more information about particular object

