
AV Evasion With Shellter
=
signature based evasion

## On-disk Evasion Techniques

- Obfuscation 
- Encoding
- Packing
- Crypters

## Shellter

/usr/share/windows-resources/shellter

requires wine

`sudo apt-get install shelter -y`
`dpkg --add-architecture i386`
`sudo apt-get install wine32`
`sudo wine shellter.exe`
operation mode auto
specify path to executable - make use of binaries in `/usr/share/windows-binaires/` like vncviewer
enable stealth mode
select payload, or make a custom with msfvenom and encode it
fill information

Invoke-Obfuscation
=
open source powershell v2 command and script obfuscator on github

powershell can be run on kali: `pwsh`

after launching pwsh, import the invoke-obfuscation.psd1:

`Import-Module Invoke-Obfuscation.psd1`

then run:

`Invoke-Obfuscation`

save a powershell payload from payloadallthethings and save the code portion into a file.ps1

`SET SCRIPTPATH /path/to/script`

use ast obfuscation technique

send to target and setup handler


