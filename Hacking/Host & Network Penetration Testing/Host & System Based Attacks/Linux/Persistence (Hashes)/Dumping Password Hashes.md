password hashes require cracking for use on linux

need root access

Methodology
=
`cat /etc/shadow` - dump shadow hashes
or
`post/linux/gather/hashdump` - dumps hashes and saves unshadowed file

Brief
=
- all users stored in the /etc/passwd file
- all encrypted passwords are stored in /etc/shadow

Hash Prefixes
=
- $1 - MD5
- $2 - Blowfish
- $5 - sha-256
- $6 - sha-512