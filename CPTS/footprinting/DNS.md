
# Footprinting

Footprinting DNS is done as a result of the requests we send.

DNS can be queried as to which other name servers are known - done using NS records and specifying which DNS server we want to query using `@`:
```
dig ns inlanefreight.htb @dns_server_ip
```

It is also possible to query the dns servers version through use of CHAOS and TXT query - however the entry must exist on the server:
```
dig CH TXT version.bind target_ip
```

We can use `any` query type to view all available records, though not all entries from the zones will be shown:
```
dig any inlanefreight.htb @dns_server_ip
```

we can possibly determine an administrators email with the SOA record using dig:
```
dig soa <domain>
```

```
;; AUTHORITY SECTION:
inlanefreight.com.      900     IN      SOA     ns-161.awsdns-20.com. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400

```

for the administrators email we would replace the (.) with an (@) in the email address `awsdns-hostmaster@amazon.com`

we can query for zone transfers - this will give us all the entries and information for a zone:
```
dig axfr inlanefreight.htb @10.129.14.128
```

if there is a misconfiguration like - the administrator using a subnet for the allow-transfer option for testing or a work around, or set to any, everyone would query the entire zone file at the DNS server, and other zones can be queried which may even show internal addresses and hostnames:
```
dig axfr internal.inlanefreight.htb @10.129.14.128
```

We can also make using for bruteforcing to find domains through dns:
```
for sub in $(cat /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done
```

this is one way for bruteforcing, there are better tools and options. for example - `DNSenum`:
```
 dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb
```


# Basics

DNS is a system for resolving computer names into IP addresses. It does not have a central database. Globally distributed DNS servers translate domain names into IP addresses and therefor control which server a user can reach via a particular domain.

Types of DNS servers used world wide:
- DNS root server
- Authoritative name server
- Non-authoritative name server
- Caching server
- Forwarding server
- Resolver

DNS is mainly unencrypted - this poses an issue where devices on the WLAN can spy on DNS queries (this is a privacy risk). There are now solutions for DNS over TLS, DNS over HTTPS, and DNSCrypt.

DNS also stores and outputs additional information about the services associated with a domain. This means a DNS query can also be used to determine which computer serves as the e-mail server for the domain, or what the domains name servers are called.

![[Pasted image 20240922143547.png]]

## DNS Root Server

These are responsible for the TLD (top level domains). As the last instance, they are only requested if the name server does not respond.

It is a central interface between users and content on the internet.

There are 13 of these across the globe, coordinated by ICANN.

## Authoritative Nameserver

these hold authority for a particular zone. Only answering queries from their area of responsibility, and their information is binding.

If an authoritative name server cannot answer a clients query, the root name server takes over.

## Non-authoritative Nameserver

These servers are not responsible for a particular zone, they collet information on specific DNS zones themselves recursively or iteratively DNS querying.

## Caching DNS server

cache information from other name servers for specific periods of time. Authoritative nameserver determines the duration of this storage.

## Forwarding Server

Only performs one function - forwards DNS queries to another DNS server.

## Resolver

perform resolution locally in the computer or router.

# DNS Records

|**DNS Record**|**Description**|
|---|---|
|`A`|Returns an IPv4 address of the requested domain as a result.|
|`AAAA`|Returns an IPv6 address of the requested domain.|
|`MX`|Returns the responsible mail servers as a result.|
|`NS`|Returns the DNS servers (nameservers) of the domain.|
|`TXT`|This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam.|
|`CNAME`|This record serves as an alias for another domain name. If you want the domain www.hackthebox.eu to point to the same IP as hackthebox.eu, you would create an A record for hackthebox.eu and a CNAME record for www.hackthebox.eu.|
|`PTR`|The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.|
|`SOA`|Provides information about the corresponding DNS zone and email address of the administrative contact.|

# Default Configuration

There are many DNS configurations.

All DNS server work with 3 different types of config files.
- local DNS configuration files
- zone files
- reverse name resolution files

`Bind9` is often used on linux. 

Its local config file (named.conf) is roughly divided:
- named.conf.local
	- define different zones divided into individual files
- named.conf.options
- named.conf.log

`named.conf` - several options that control the behavior of the name server

## Zone file

is a text file the describes a DNS zone witht he BIND file format - it is a point of delegation in the DNS tree.

A zone file describes a zone completely. 

There must be precisely one SOA record and at least one NS record.

an example zone file `db.domain.com` where all forward records are entered in bind format:
```
;
; BIND reverse data file for local loopback interface
;
$ORIGIN domain.com
$TTL 86400
@     IN     SOA    dns1.domain.com.     hostmaster.domain.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     ns1.domain.com.
      IN     NS     ns2.domain.com.

      IN     MX     10     mx.domain.com.
      IN     MX     20     mx2.domain.com.

             IN     A       10.129.14.5

server1      IN     A       10.129.14.5
server2      IN     A       10.129.14.7
ns1          IN     A       10.129.14.2
ns2          IN     A       10.129.14.3

ftp          IN     CNAME   server1
mx           IN     CNAME   server1
mx2          IN     CNAME   server2
www          IN     CNAME   server2
```

for an IP address to eb resolved from the FQDN, the DNS server must also have a reverse lookup file. In this file the computer name FQDN is assingedd to the last octet of an IP address - corresponding the the respective host using a PTR record.

example `reverse name resolution zone file` named `db.10.129.14`:
```
;
; BIND reverse data file for local loopback interface
;
$ORIGIN 14.129.10.in-addr.arpa
$TTL 86400
@     IN     SOA    dns1.domain.com.     hostmaster.domain.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     ns1.domain.com.
      IN     NS     ns2.domain.com.

5    IN     PTR    server1.domain.com.
7    IN     MX     mx.domain.com.
```

# Dangerous Settings

DNS can be attacked in many ways, including through public exploit CVEs and others https://securitytrails.com/blog/most-popular-types-dns-attacks 

The setting below may allow  these vulnerabilities:

|**Option**|**Description**|
|---|---|
|`allow-query`|Defines which hosts are allowed to send requests to the DNS server.|
|`allow-recursion`|Defines which hosts are allowed to send recursive requests to the DNS server.|
|`allow-transfer`|Defines which hosts are allowed to receive zone transfers from the DNS server.|
|`zone-statistics`|Collects statistical data of zones.|
# Dig



