
Living off the land
=
1. cat /etc/shells
2. /bin/bash -i
3. /bin/sh -i
4. python -c 'import pty;pty.spawn("/bin/bash")'
5. perl -e 'exec "/bin/bash";'
6. ruby: exec "/bin/bash"

### display env variables

if env path is not set, set it up

1. export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin/:/usr/bin:/sbin:/bin
2. export TERM=xterm
3. export SHELL=bash