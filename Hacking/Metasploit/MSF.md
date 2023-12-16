
Modules
=
- Exploit  - used to take advantage of a vulnerability paired with a payload
- Payload - code delivered by MSF
- Encoder - encodes payloads to avoid signature based AV
- NOP - ensure that payloads sizes are consistent and ensure stability
- Auxiliary - perform additions functionality like port scanning and enumeration
- Post
- Evasion

Payloads
=
Non-staged -sent with exploit
staged - needs stager and stage, stager sent with exploit, stager downloads stage
meterpreter - advanced multi function payload executed in target memory

File System
=
directory: search for  stuff you need
/usr/share/metasploit-framework/
	modules/

PTES
=
1. information gathering - Auxiliary modules
2. Vulnerability scanning - Auxiliary modules, Nessus
3. exploitation - exploit modules, payloads
4. post exploitation - meterpreter
5. Priviledge escalation - Post exploitation modules, meterpreter
6. maintaining persistence - post exploitation modules, persistence

MSFDB
=
postgresql needs to be installed and running
`sudo msfdb init`
`sudo msfdb reinit`

