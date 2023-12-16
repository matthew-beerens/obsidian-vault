discover and mount

port 445 - smb service
port 139 - netbios sets up service for smb

MIGHT BE ABLE TO CONNECT TO OTHER SERVICES THAT ARE PIPED TO SMB
=
using metaplsploit `use the auxiliary/smb/pipe_auditor`
set up user information and host
note down the pipes, its useful information

PowerShell - setup mount smb on windows machine
=
`net use z: //ip/C$ smbserver_771 /user:administrator`
	smbserver_771 is the password
	/user:administrator - sets the user
	can also be achieved with file explorer - map new drive in network portion of file explorer


useful scripts from nmap:
=
accepts arguments if found  `--script-args smbusername=user smbpassword=pass`

`--script smb-protocols` - versioning information, dialects
`--script smb-security-mode`
`--script smb-enum-sessions` - shows whos logged into the shar
`--script smb-enum-shares` - maps out shares of smb
`--script smb-enum-users `
`--script smb-server-stats` - data sent and received, how many failed logins, error
`--script smb-enum-domains` - groups, users, password information
`--script smb-enum-groups`
`--script smb-enum-services`
`--script smb-enum-shares,smb-ls` - runs the script and also ls in the shares
`--script smb-os-discovery` - exact version of smb, computer names etc

Tools:
=
SMBMap - used to enumerate and interact with smb
=

usage:
`smbmap -u guest -p "" -d . -H 10.10.10.10 -x 'ipconfig'`
	-u user
	-p password
	-d directory
	-H host ip
	-x command
	-L list drives apart of the smb
	-r 'C$' connect and list contents of drive
	--upload 'path to file' 'path to save file' | --upload '/path/backdoor'  'C$\\file'
	-- download 'C$\\flag.txt'

basic map using user - `smbmap -H IP -u username -p pass` - shows read write permissions

sometimes wont list shares even if permissions are there, use smbclient to connect and manually check for shares

msfconsole - samba
=
`use auxiliary/scanner/smb/*` - check out the available tools for samba and smb recon
1. smb_version
2. smb_enumshares (smb_enum*TAB)
3. smb_login - brutforce smb login

`use auxliary/scanner/smb/smb2` - enumerates smb2 protocol

nmblookup - samba UDP

usage:

`nmblookup -A ip`
- SAMBA-RECON      <20> = server we can connect to, CONNECT WITH SMB CLIENT



smbclient
=
usage:
`smbclient -L IP -N - list shares with no pass/null session`
`smbclient //ip/share -N - connect to share as null session`
	get - download file



Enum4linux 
=
usage:

`enum4linux IP`
	-o operating system information
	-U enumerate for users
	-S enum shares
	-G enum groups
	-i search for printers
	-r -u user -p pass - finds ssid of users


rpcclient
=

usage:

`rpcclient -U "" -N IP` - connects to samba/smb with a null session - no errors, successful connect

rpcclient commands:
	`srvinfo` - versioning information other service information
	`enumdomusers` - list of users
	`lookupnames username` - full SID of user
	`enumdomgroups` - enum groups


Hydra for bruteforcing smb
=
usage:
`hydra -l username -P wordlist IP smb`
