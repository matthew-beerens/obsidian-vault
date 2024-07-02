
We should search the systems for exposed confidential information. We can sometimes find this information in the users .files or other places like env variables.



### Inspecting User Trails

`cat .bashrc` - search the bashrc file for information, this bashrc is most likely setting env variables

`env` - search the environment variables for sensitive information

`if we find some credentials we could use su - root or sudo -i if we have sudo privs to attempt to elevate privs`


### Inspecting Service Footprints

a lot of the time adhoc system services may be used by administrators for tasks, and sometimes neglect security best practices.

In linux we can list higher-prived processes.

`watch -n 1 "ps aux | grep pass"` - refresh the ps command every second to inspect running processes containing the work pass in hopes of catching an easy password

`sudo tcpdump -i lo -A | grep "pass"` - sometimes user have access to tcpdump, we can use this to our advantage to sniff packets for passwords.
- this example uses:
	- -i lo for the loopback interface
	- -A for the format to be ASCII

