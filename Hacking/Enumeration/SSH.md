remote administration secure shell
port 22

Commands
=
banner-grab: nc IP PORT

nmap:
--script
	`ssh2-enum-algos` - enumerate ssh algorithms used to create key
	`ssh-hostkey --script-args ssh_hostkey=full` - gives full ssh-rsa hostkey
	`ssh-auth-methods --script-args="ssh.user=USERNAME"` - shows authentication methods - might show no method, can login without creds
	`ssh-run --script-args="ssh-run.cmd=COMMAND, ssh-run.username=username, ssh-run.password=password"` - put blank password if no method of authentication

Helpful
=
connect to ssh normally with **ssh IP** to fetch pre-login banner

Brute-forcing
=
`hydra -l username -P wordlist host ssh`
`nmap host -p 22 --script ssh-brute --script-args userdb=/root/user(userlist)`
**msfconsole** `auxiliary/scanner/ssh/ssh_login` - can set user pass file with /usr/share/wordlists/metasploit/root_userpass.txt to find root user password