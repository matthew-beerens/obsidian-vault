
## Methodology

1. public domain information gathering `whois`
2. check tls certificate for sub domains/other domains
3. use dig or ns lookup to gather dns information (user both, you may recieve different information from each)
	1. nslookup -query=ANY target
	2. dig any @1.1.1.1 target
4. use combination of dig/nslookup and whois to determine if target is using hosting providers
5.  [https://censys.io](https://censys.io/) [https://crt.sh](https://crt.sh/) used for finding subdomains by analyzing tls certificates
6.  automate process:
	1. `curl -s "https://crt.sh/?q=${TARGET}&output=json" | jq -r '.[] | "\(.name_value)\n\(.common_name)"' | sort -u > "${TARGET}_crt.sh.txt"`
	2.  `head -n20 facebook.com_crt.sh.txt`
7. use TheHarvester for information gathering using  sources:
	1. baidu
	2. bufferoverun
	3. crtsh
	4. hackertarget
	5. otx
	6. projecdiscovery
	7. rapiddns
	8. sublist3r
	9. threatcrowd
	10. trello
	11. urlscan
	12. vhost
	13. virustotal
	14. zoomeye
8. utilize netcraft https://sitereport.netcraft.com
	1. check recent ips for an ip that might have existed before being places behind a load balancer
9. utilize wayback machine, sometimes you can see that out of date plugins were used and were not removed properly that might now be vulnerable, it can also be used to find forgotten about pages, assets that may be vulnerable
10. waybackurls command can be used to find possibly all urls related to a domain at one point in time - this requires go setup