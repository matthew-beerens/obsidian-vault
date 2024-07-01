
#### File Permissions

Everything in a nix like system is a file. The whole operating system is represented in the filesystem.
Every file in nix have the:
- owner permissions
- owner group permissions
- others group permissions

For files:
- r - read the files
- w - write to the file
- x - execute the file
For directories:
- r - list the dirs contents
- w - create/delete files within the dir
- x - enter the directory and access its contents

`Being able to cross through a directory without being able to read it gives the user permission to access known entries, but only by knowing their exact name`

`the first - in a files permissions listing described the file type`.


```
-rw-r----- 1 root shadow 1751 May  2 09:31 /etc/shadow
```

in the above example: root has read/write, shadow has read, and others group has no permissions.


##### SUID & GUID

if these special writes are set on a file, it will be present in the permissions as an "s" or an "S". This allows the user to execute the files with rights of the owner (setuid) or the owners group (setgid)


#### Manual Enumeration

Manual enumeration cannot be replaced by automated enumeration, and both should be used.

##### Initial enumeration

`id` - information about the current user context, their uid, gid, and their groups.

`cat /etc/passwd` - list all the users on the system.
- output from this command: `joe:x:1000:1000:joe,,,:/home/joe:/bin/bash`
	- joe - login name
	- x - hashed version of the password - x signifies that the has is in the /etc/shadow
	- 1000 - uid
	- 1000 - gid
	- joe,,, - comment
	- /home/joe - users home directory
	- /bin/bash - default shell on login

`hostname` - can be used to find out a machines intended use

`cat /etc/issue && cat /etc/os-release && uname -a` - operating system information including os, release version, kernel version and architecture.

`ps aux` - list the running processes with or without a tty and in a user readable format, the goal is to find a process that insecure permissions or that we can interact with in unintended ways to gain a privesc. `Research these processes for vulns`. you can also filter for user specific processes.

`ip a || ifconfig a` - list the network interfaces attach to the machine, may reveal other networks we can pivot to.

`route || routel` - display routing tables

`ss -anp || netstat -anp` - display active network connections and listening ports

`iptables` - usually needs root access but can give up information about firewall rules. We can gain information about firewall rules from different location including:
- /etc/iptables/rules.v4
	- files like these often have weak permissions and can reveal firewall rulles
	- files like this are a result of the
		- iptables-persistent package
			- used to restore netfilter rules at boot
		- iptables-save command
		- iptables-restore command

`ls -lah /etc/cron*` - list all cron job folders on the system.

`crontab -l` - user admin cronjobs that usually run as root that we could exploit
- try also running `sudo crontab -l` - this will reveal scripts that run as root if the user does not have any at their user level.

`dpkg -l` - list installed packages and software in an attempt to find versions that can be exploited with public exploits. `Different flavours of linux may have different commands for doing this e.g. redhat uses rpm`

`find / -writable -type d 2>/dev/null` - list all writable directories on the system


drive mounts - we should always check drive mounts with weak permissions for stashes of valuable information, we should also attempt to mount drives that have no yet been mounted at boot.

`cat /etc/fstab` - list all drives that will be mounted at boot time. This is useful for using with `mount` to determine what other drives are on the system that are not mounted at boot time that may have useful information.

`mount` - list all mounted filesystems.

`lsblk` - list all available disks

`lsmod` - enumerate the loaded kernel modules (drivers), to find kernel modules we might be able to exploit
`/sbin/modinfo <module>` - get more information about a kernel module we found interesting

`find / -perm -u=s type f 2>/dev/null` - search for all files in the system with suid or guid set with the goal of executing a command or script as root.

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md

https://book.hacktricks.xyz/linux-hardening/privilege-escalation


#### Automated Enumeration

`./unix-privesc-check standard | detailed > output.txt` - local bash script on our kali system `/usr/bin/unix-privesc-check` that can be used to to a baseline enum

`LinEnum`

`LinPeas`

`Although these tools perform many automated checks, we should bear in mind that every system is different, and unique one-off system changes will often be missed by these types of tools. For this reason, it's important to check for unique configurations that can only be caught by manual inspection, as illustrated in the previous section`

