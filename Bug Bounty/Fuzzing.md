
### Directory Fuzzing

#### ffuf

fuzz for common extensions
fuzz directories with found extension

`ffuf -w /usr/share/wordlist:FUZZ,/usr/share/wordlist:ZZUF -u http://url/FUZZ/ZZUF`
	`-recursion -recursion-depth 1` - recursive fuzz
	`-e .php` - specify extension
	`-v` - full directories url to make it easier to tell which directory belongs where


### Domain Fuzzing

#### sub-domains

`ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.hackthebox.eu`

#### vhosts

vhosts are served on the same server under the same IP, may or may not have a public dns record

scan in conducted fuzzing the host header of a http/s request

`ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'`
	-H - header value

### Parameter Fuzzing

Fuzzing parameters may expose unpublished parameters that are publicly accessible. Such parameters tend to be less tested and less secured, so it is important to test such parameters for the web vulnerabilities we discuss in other modules.

#### GET Request Fuzzing

`ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx`

#### POST Request Fuzzing

In PHP, "POST" data "content-type" can only accept "application/x-www-form-urlencoded"

`ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx`

test request from positive result "id":
`curl http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=key' -H 'Content-Type: application/x-www-form-urlencoded'`

#### Value Fuzzing

creating custom wordlist for fuzzing values of a parameter:

`for i in $(seq 1 1000); do echo $i >> ids.txt; done` - generates value of id parameter 1-1000

fuzzing parameter value:
`ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx`