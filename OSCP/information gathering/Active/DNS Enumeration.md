
### Host Command

This command can be used to enumerate DNS.

`host <url>` - can be used to find the IP address of domain. This command queries for an A record

`host -t mx <url>` - can be used to query for mail servers connected to a domain. It will list the priorities and the corresponding mail servers. The lowest priority number the mail server to be used first to forward mail.

`host -t txt <url>` - can be used to query the DNS for arbitrary data within text files on the server.

`for ip in $(cat list.txt); do host $ip.<url>.com; done` - This is a bash one-liner that can be used to brute-force DNS to find function domains/servers belonging to a companies domain. It requires a wordlist. SecLists has good wordlists for brute-forcing. 

`We may find an IP range and test that IP range in hopes that the administrator has configured PTR records, so that we can reverse query the DNS to reveal more domains within the IP range.`

`for ip in $(seq 200 254); do host <ip_prefix>.$ip; done | grep -v "not found"` - This bash one-liner will look for domains connected to these IPs within the IP range. If we find a domain, and reveal its IP, we may find other domains in the range with this scan.


### DNSRecon Tool

This tool can be used to automate DNS enumeration.

`dnsrecon -d <url> -t <type>` - basic usage

`dnsrecon -d <url> -t  std` - standard enumeration of DNS. Will reveal all records available on DNS excluding axfr.

`dnsrecon -d <url> -t brt -D <path_to_list>` - This is a dnsrecon brute-force methods that makes use of a dictionary to brute-force additional host names.

### DNSEnum Tool

This tool can be used to automate/brute-force DNS enumeration.

`dnsenum <url>` - basic usage for brute-force.

### nslookup

This is a windows command line tool for enumerating DNS.

`nslookup <domain/url>` - basic usage to resolve the A record for a domain.

`nslookup -type=TXT <domain/url> <IP>` - Run a TXT DNS query against a domain of a specific host.

LEARN HOW DO TO THE BASH ONE-LINERS ABOVE IN POWERSHELL