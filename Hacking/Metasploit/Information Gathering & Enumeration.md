check out metasploit autopwn plugin from github to automate pwning process after enum

Methodology (scanning and pivoting/routing)
=
port scanning:
1. `search portscan`
2. select tcp portscan and run portscan on target
3. identify open ports
4. research open ports
	1. if http port open run a `curl` command to inspect technology, headers, page
	2. research http technologies for exploits
	3. exploit if available
5.  once shell is obtained on target system look for more hosts
	1. run ifconfig to find network id, subnet, etc
	2. if more hosts run autoroute command from meterpreter
	3. background the session - FAILURE TO BACKGROUND THE SESSION WILL REMOVE THE ROUTE CREATEDhosts
6. run tcp portscan again on other discovered host after adding route to system
7. continue to scan udp ports afterwards `udp_sweep` module

Curl
=
1. `curl ip` - make web requests when restricted to console

Autoroute
=
`run autoroute -s next_target_ip` - adds a route from attacker through exploited hosts to another host

FTP
=
`search type:auxiliary name:ftp` - to find scripts for ftp scanning

using auxiliary/scanner/ftp/*
1. `ftp_version`
2. `ftp_login`
3. `anonymous`
search for exploits after information enumeration

SMB
=
`search type:auxiliary name:smb`
`smb_version`
`smb_enumusers`
`smb_enumshares`
`smb_login` -bruteforce

Web Server
=
`search type:auxiliary name:http`
`http_version`
`http_header`
`robots_txt`
`dir_scanner` - dir brute
`files_dir` - file brute
`http_login` 
`apache_userdir_enum` - username enumeration for apache

MySQL
=
`search type:auxiliary name:mysql`
`mysql_version`
`mysql_login`
`mysql_enum` - really useful, use it
`mysql_sql` (or just login with mysql tool)
	commands:
	`show databases();`
	`use database_name()`
`mysql_schemadump`

SSH
=
`search type:auxiliary name:ssh`
`ssh_version`
`ssh_login` or `ssh_login_pubkey` - bruteforece user:pass or key based authentication in ssh - will initialize a session shell
`ssh_enumusers`

SMTP
=
`search type:auxiliary name:smtp`
`smtp_version`
`smtp_enum` - can use users found to bruteforce ssh

connect with msf connect or netcat
commands:
`VRFY username` - check whether user exists on server
`EHLO username` - check supported commands and capabilites for user

send mail:
`sendemail -f admin@attacker.xyz -t root@openmailbox.xyz -s 192.26.29.3 -u Fakemail -m "Hi root, a fake from admin" -o tls=no `

Install Autopwn
=
1. wget raw from github
2. `sudo mv db_autopwn.rb /usr/share/metasploit-framwork/plugins/`
3. `load db_autopwn` in msfconsole
4. `db_autopwn -p -t -PI SPECIFIC_PORT` enumerate all exploits on open ports

Analyze (msfconsole command)
=
will search the db in msf to see while vulns are usable for hosts

Nesus
=
1. export scan from nessus
2. import nessus scan into msfconsole `db_import /path/file.nessus`
3. confirm with `hosts` & `services`
4. `vulns`
5. `vulns -p port`

WMAP
=
automate web application scanning with msf
1. `load wmap`
2. `wmap_*`
	1. modules
	2. nodes
	3. `run -t`
		1. `-e`  - use all enabled modules
	4. `sites -a IP addr` - creates site for host in msf
		1. `-l` list the sites
	5. `targets -t http://target_ip/directory`- set target urls for site
	6. `vulns -l`

HTTP
=
`use scanner/http/http_put`
`use scanner/http/http_options`
use curl to test uploads