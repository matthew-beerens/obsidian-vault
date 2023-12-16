
SAM database is ecrypted with syskey

sam cannot be  copied while system is running

copies are made during windows repair and backups, sometimes needs to be deleted manually

lsass holds a cache of sam databasae file

NTLM hashes are hashed with MD4

## Dumping

use meterpreter or mimikatz

meterpreter:
1. migrate into lsass process
2. load kiwi or hashdump
3. copy hashes into text file
4. check net user to see if we got all hashes

## Cracking

### John

`john --format=NT hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt`

### Hashcat

NTLM = 1000

`hashcat -a3 -m 1000 hashes.txt --wordlist /usr/share/wordlists/rockyou.txt`