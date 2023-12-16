Popular db on linux systems

port 3306

Commands
=
`mysql -h host -u user(root)` - login to mysql sb
	help
	show databases;
	use database_name;
	show tables;
	use table_name;
	select * from table_name;
	select load_file("/file/name"); - loads a file from the linux system, could be /etc/files

nmap scripts
=
--script
	`mysql-empty-password` - gets accounts that can login without passwords/anonymous login
	`mysql-info` - versioning, capabilites (look for interactive client)
	`mysql-users --script-args="mysqluser='root', myqlpass=''"` - dumps mysql users on the db
	`mysql-databases --script-args="mysqluser='root', myqlpass=''"`
	`mysql-varaiables --script-args="mysqluser='root', myqlpass=''"` - look at data directory (datadir), shows where data is stored
	`mysql-dump-hashes --script-args="username='root', password=''"` - dumps users and their hashes that are on the dbms, hash is after the second set of 
	`mysql-audit --script-args="mysql-audit.user='root', mysql-audit.password=' ', mysql-audit.filename='/usr/share/nmap/nselib/data/mysql-cis.audit'"` - useful information, tests configurations of database
	`mysql-query --script-args="query='query here' username='root', password=''"`


Msfconsole
=
`auxiliary/scanner/mysql/*`
	`mysql_writable_dirs`
	`mysql_hashdump` - dumps hashes in db
	`mysql_schemadump `- dumps all table and columbs

Bruteforcing
=
msfconsole - `auxiliary/scanner/mysql/mysql_login`
`hydra -l username -P passwordfiles host mysql`