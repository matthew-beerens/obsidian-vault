
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

#### Installed/Running Applications

`Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname` display all 32 bit installed applications on the system

`Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname` display all 64 bit installed applications on the system

*we should always check the 32bit and 64bit program files directories and downloads due to flawed installation processes*

`Get-Process` identify running programs and processes on the system

`Get-Process -Id 8444 | Select-Object -Property ProcessName, Id, WS, Path` - get more information about particular object


#### Files on the system

After researching applications installed on the system we can search for any relevant files containing information such as configuration files. We may be able to find plaintext credentials or other relevant information to further assist in escalating privileges.

`Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue` search for password manager database files

`Get-ChildItem -Path C:\xampp -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue` example of searching for sensitive files in an xampp installation.

`Get-ChildItem -Path C:\Users\dave\ -Include *.txt,*.ini,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue` example of searching for files that may have sensitive information in a users home directory on the system - IF OUTPUT IS TOO MUCH REMOVE FILE TYPES

`net user <user>` learn further about the user before we leverage information collected using the above methods.

*after gaining access to a new user with information we have leveraged the process begins again, and we repeat what we have done previously to advance further through the system,*

`We may now have new privileges and should recap on what we previously could not access, such as files we did not have permission to access.`


#### Powershell History and Logging

In enterprise environments it common to find powershell logging mechanisms enabled on windows clients and servers. `PowerShell Transcription` and `PowerShell Script Block Logging`.

`Transcription` is over-the-shoulder-transcription. Logging information is equal to what you would see looking at someone's screen running commands. The information is stored in `transcript files`. These files are often saved in the home directories of users, central directory for all users or a network share collecting the files from all configured machines.

`Script Block` records commands and blocks of script code as events while executing. This is much broader logging. It records the full content of code and commands. Including original representation of encoded code or commands.

`Get-History` retrieve the powershell history of the current user (if this is clear `Clear-History` may have been used, but this only clears PowerShell's own history)

`(Get-PSReadlineOption).HistorySavePath` get history information path location from PSReadline module, `Clear-History`  does not clear this files. Read this information and analyze carefully to gain hidden information. If some cases we may be able to repeat the steps we find in this file to gain access to another user with elevated privileges. 

`Applications and Services Logs > Microsoft > Windows > PowerShell > Operational` - we can view these logs in the event view if we have an rdp connection to attempt to harvest credentials.

*Administrators can prevent PSReadline from recording commands by setting the -HistorySaveStyle option to SaveNothing with the Set-PSReadlineOption Cmdlet. Alternatively, they can clear the history file manually*

#### New Users Obtained

If a user does not belong to the `RDP` group we will not be able to login to the user. 

We will need GUI access to use the user account. If we already have access to a user with `RDP` group, we can leverage this user through use of the `Runas` command. 

`Runas` can be used as long as the user has ability to logon to the system. 

This can be used with `local or dommain` accounts.

#### Automated Enumeration

We can use tools like `WinPEAS`, `Seatbelt`, and `JAWS` to automated the above methods to get a comprehensive view of the system.

Automated tools can be blocked by AV so if this doesn't work resort to manual or other automated tools.

`sudo apt install peass` - install the peas tools on kali

`cp /usr/share/peass/winpeas/winPEASx64.exe .` win peas can be found at this directory and copies to the current directory for ease of usage and serving to target host