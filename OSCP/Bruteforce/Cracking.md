
#### Methodology

1. Extract hashes
2. Format hashes
	1. use `hashid` or `hash-identifier` to determine hash types
3. Calculate cracking time
4. prepare wordlist
5. attack the hash

#### Calculating Time-To-Crack

We calculate time to crack using the key space to the power of password length

If we have lowercase (26) and uppercase (26) and numbers from 0-9 (10) in the password we have 62 possible characters.

For a password of length 5 we would have 62^5 (916,132,832) - possible permutations (key space)

We then divide this number by the hash rate to see how long this would take.

`hashcat -b` running hashcat in benchmark mode to get the hashrate. (1MH/s = 1,000,000/s)

If we have a hashrate of 134.2MH/s we would calculate the time to crack with `916,132,832/1342000000` = ~ 7 seconds to try all possible combinations

Increasing the length of password increases the time to crack exponentially, but increasing the key space only increases by polynomial time. 

#### Mutating Wordlists

We can create a rule file to use with our cracking tools to mutate passwords to appease password policies.

`sed -i '/^1/d' demo.txt` - remove all passwords from a  wordlist that starts with the number 1. We can use this  to remove passwords that we definitely cant use even with mutilation. `-i` does this in place  - no shifting/moving position

The hashcat wiki can then be  used to list the possible password rules.

useful rules (if rules are on the same line they are treated as 1 rule, else two separate rules and processed separately with duplication):
`$<char>` append a character
`^<char>` prepend a character
`c` capitalize first letter of password (if new separate line will duplicate password and capitalize else will just capitalize every password)
`:` do nothing - good for keeping duplication if you only have one rule
`u` all characters uppercase
`d` duplicate password (123 becomes 123123)

`ls -la /usr/share/hashcat/rules/` HASH CAT HAS ITS OWN RULES YOU CAN PICK FROM

`echo \$1 > demo.rule` - append 1 to every password rule

`hashcat -r demo.rule --stdout demo.txt` use `demo.rule` (rule list) with `demo.txt` (password list) to mutilate password list - this just prints it

`hashcat -m 0 crackme.txt /usr/share/wordlists/rockyou.txt -r demo.rule --force` - crack and md5 with a password rule ignoring warning messages.

`When attempting to create rules to mutate an existing wordlist, we should always consider human behavior and convenience with regard to passwords. Most users use a main word and modify it to fit a password policy, perhaps appending numbers and special characters. When an upper case letter is required, most users capitalize the first letter. When special characters are required, most users add the special character at the end of the password and rely on characters on the left side of the keyboard since these digits are easy to reach and type`


#### Cracking Password Managers

We should look in the `settings>Apps>Apps & features` for any password managers, do some research on the managers if we find any and then further enumerate with that information.

`Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue` find all `.kdbx` files on the system (password databases used by password managers)

`keepass2john Database.kdbx > keepass.hash` correct formatting for cracking (REMOVE THE FILENAME PREPENDED TO THE OUTPUT IN HASH - this is for usernames, this is a master password so no usernames are used)

`hashcat --help | grep -i "KeePass"` how to use keepass cracking

`hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force` example of cracking keepass database using the rockyou rule list provided by hashcat


#### SSH Private Key Passphrase Cracking

1. `ssh2john id_rsa > ssh.hash` prepare private key for cracking

2. `cat ssh.hash` - we can see the type of hash used as the second parameter (second $)

3. `hashcat -h | grep -i "ssh"` - find the correct cracking method to use based on parameter found
4. prepare rules if needed
		1. `[List.Rules:sshRules]` prepend this to rule file for john
		2. `sudo sh -c 'cat /home/kali/passwordattacks/ssh.rule >> /etc/john/john.conf'` - how to add rules to `john`
6. `hashcat -m 22921 ssh.hash ssh.passwords -r ssh.rule --force` attempt to crack key
7. if hashcat doesn't work for one reason or another use `john`
	1. `john --wordlist=ssh.passwords --rules=sshRules ssh.hash`

`ssh -i id_rsa -p 2222 dave@192.168.50.201` connect using private key

`/home/user/.ssh/id_rsa` - location of private key


