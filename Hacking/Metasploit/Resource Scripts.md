located /usr/share/metasploit-framework/scripts/resource/

Creating RC script
=
create file.rc

this script would automate setting up handler

`use mutli/handler`
`set PAYLOAD payload`
`set LHOST`
`set LPORT`
`run`

`msfconsole -r handler.rc` - uses the script created 
`resource path/to/script` - uses script from within msfconsole
`makerc /path/to/save.rc` - creates a script from entered commands