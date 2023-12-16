$1 - MD5
$2 - Blowfish
$5 - SHA-256
$6 - SHA512

user information stored

/etc/passwd
/etc/shadow

### msfconsole dump hashes

`use post/linux/gather/hashdump` module - dumps unshadowed hashes for cracking

### john

adjust format accordingly
`john --format=sha512crypt hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt`

### Hashcat

`hashcat -a3 -m 1800 hashes.txt /wordlist` - cracking sha512