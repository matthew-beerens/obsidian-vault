 
## Methodology:
1. determine web server version, IIS version may indicate which windows operating system is running
2. curl -I grab headers, determine what the website is powered by
	1. check the cookies, some tech stacks has its own session id cookies
3. use whatweb to fingerprint the website
	1. -a3 - to set aggressive level
	2. -v - verbose can be useful to get more insight\
4. use wappalyzer 
5. use wafwoof to fingerprint waf
6. use aquatone - for scanning and dealing with huge subdomain lists
	1. it will provide a report
7. check zonetransfers https://hackertarget.com/zone-transfer/
	1. run a nslookup for nameservers on the domain `nslookup -type=NS zonetransfer.me`
	2. then run any + axfr nslookup with domain and nameserver `nslookup -type=any -query=AXFR zonetransfer.me nsztm1.digi.ninja`
8. enumerate subdomains with gobuster using found naming patterns (create a patterns .txt with the patters)
	1. example pattern `lert-api-shv-{GOBUSTER}-sin6` - can use {NUMBER} for enumerating numbers
	2. grab the seclists repository wordlists
	3. example - `gobuster dns -q -r "${NS}" -d "${TARGET}" -w "${WORDLIST}" -p ./patterns.txt -o "gobuster_${TARGET}.txt"
		1. q - quite mode, omits banners
		2. -r - custom dns server
		3. -d domain
		4. -w wordlist
		5. -p - patterns file
		6. -o output file
9. fuzz for virtual hosts using a dns namelist, seclists has one
	1. using ffuf -  `ffuf -w ./vhosts -u http://192.168.10.10 -H "HOST: FUZZ.randomtarget.com" -fs 612`
		1. -fs size - discard all default reponses
	2. curl -s target -H "HOST: vhost"
10. crawl a website using owasp spidering by opening fire fox with zap, add the site to scope and use the attack>spider
11. use fuff to find other contetn, zap only spiders through links it finds `ffuf -recursion -recursion-depth 1 -u http://192.168.10.10/FUZZ -w /opt/useful/SecLists/Discovery/Web-Content/raft-small-directories-lowercase.txt`
12. try to find sensitive files with cewl and fuff
	1. cewl will scrape a webpage for words `cewl -m5 --lowercase -w wordlist.txt http://192.168.10.10` - get words with minimum 5 len
	2. `ffuf -w ./folders.txt:FOLDERS,./wordlist.txt:WORDLIST,./extensions.txt:EXTENSIONS -u http://192.168.10.10/FOLDERS/WORDLISTEXTENSIONS` - this makes use of multipe wordlists, including folders, wordlists and extensions


helpful  tips
dig axfr @ip_of_domain zone nameserver  - enumerate a zone for axfr