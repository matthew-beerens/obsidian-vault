domain name servers are for mapping ips to domain names

Popular DNS:
cloudflare 1.1.1.1
google 8.8.8.8

Record Types:
A - ipv4
AAAA - ipv6
NS -reference to domains nameserver
MX - mail server
CNAME - domain alias
TXT - text record
HINFO - host info
SOA - domain authority
SRV - service records
PTR - ip to hostname resolution
AXFR - zone transfer

Zone Transfer - copies dns records from one dns to another, includes extra records that are not available with passive collection methods, includes internal information not publically available

Tools:
DnsEnum - bruteforce dns records, get ips, mails servers, zonetransfer records
Dig - dns lookup utility
fierce - ip space and hostname scanner against specified domains, can be used for dns zone transfer with -dns flag

