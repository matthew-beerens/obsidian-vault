
#### Methods

`GUI Access` we can leverage a new user if the are members of the RDP group

`WinRM Access` we can leverage a new user if they are members of the WinRM group

`Log on as a batch job` if the user has this access right we can utilize a scheduled task to execute a program as this user.

`Actice session` if the user already has an active session we can use `PsExec` from `sysinternals` to gain access.

`Runas` if we have GUI access as another user we can utilize run as to run programs as another user

#### Commands

`runas /user:backupadmin cmd` run cmd as another user, we will be prompted for password with a password prompt (why we need GUI access).

`evil-winrm -i 192.168.50.220 -u daveadmin -p "qwertqwertqwert123\!\!"` login to remote system using WinRM with harvested credentials
