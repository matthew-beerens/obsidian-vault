stores files on server to be access remotely

port 21

Commands
=
`help `- command to list ftp commands
`bye` - to disconnect
`get` - download file

nmap scripts
=
`--script`
	`ftp-brute --script-args userdb=/path/userlist  -p PORT` - bruteforces ftp with nmap
	`ftp-anon` - enum for anonymous login vuln

Methods
=
bruteforce with hydra username list and password list if no info found and if nmap brute doesnt work

Misconfigurations
=
anonymous login - user: anonymous : pass empty