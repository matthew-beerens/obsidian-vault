
Most popular SQL databases include:
1. MySQL
2. Microsoft SQL Server
3. PostgreSQL
4. Oracle

#### Connecting and Interacting with MySQL server

`mysql -u root -p'root' -h 192.168.50.16 -P 3306` - connect to a remote MySQL server through the command line.

`select version();` - display the MySQL version
`select system_user();` - display the current database user and the connection IP.
`show databases;` - list all databases.
`select table_name from information_schema.tables where table_schema='mysql;'` - print all tables within a database e.g. 'mysql'.
`show columns from database.table;` - show all columns of table within database.
`select user, authentication_string from mysql.user where user = 'offsec';` - example selection query which searches for the offsec user in the MySQL database user table.


#### Connecting and Interacting with MSSQL

you can connect to the server on windows using `SQLCMD` or `impacket` on kali.

`When using a SQL Server command line tool like sqlcmd, we must submit our SQL statement ending with a semicolon followed by _GO_ on a separate line. However, when running the command remotely, we can omit the GO statement since it's not part of the MSSQL TDS protocol.`

`impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth` - connect to a remote MSSQL server from kali.

`select @@version` - display the current MSSQL version running on the server and the version of the underlying operating system.
`select name from sys.databases;` - list all available databases. master, tempdb, model, msdb are all default databases.
`select * from offsec.information_schema.tables;` - list the tables in the offsec database.
`select * from offsec.dbo.users;` - display all the users in the users table within the offsec database. the dbo is used between the database and the tables.
`select * from master.dbo.sysusers;` - display all system users; 
