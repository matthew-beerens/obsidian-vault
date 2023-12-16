Popular microsoft sql dbms
port 1433

Nmap scripts
=
`--script`
	`ms-sql-info`
	`ms-sql-ntlm-info --script-args mssql.instance-port=1433` - more sql info
	`ms-sql-brute --script-args userdb=/path/to/userlist, passdb=/path/to/passwd.lst`
	`ms-sql-empty-password`
	`ms-sql-query --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-query.query="select * from master..syslogins" -oN output.txt `- services, sys user logins etc
	`ms-sql-dump-hashes --script-args mssql.username=admin,mssql.password=password`
	`ms-sql-xp-cmdshell --script-args mssql.username=username,mssql.password=password,ms-sql-xp-cmdshell.cmd="ipconfig"`

Metasploit
=
`auxiliary/scanner/mssql/*`
	`mssql_login`

`auxiliary/admin/mssql/*`
	`mssql_enum`
	`mssql_enum_sql_logins`
	`mssql_enum_domain_accounts`
	`mssql_exec` - execute commands on system
