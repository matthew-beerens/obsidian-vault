
The domain is about the entire presence on the internet. The goal is to gather information to understand the targets functionality and which tech and structures are necessary for services.

The first step in gathering information about the domain of the target is scrutinizing the targets main website. Read through it, understanding the company and envisioning what technology and structures are required for what they offer.

following these steps will give us a good overview and allow us to assess the targets internet presence.

# Online Presence


## SSL/TLS certificate 

the first point of presence on the internet may be the SSL certificate. From here we may be able to gather information about the companies domains through the SAN list on a SSL certificate.

We can also make use of `crt.sh` This source is Certificate Transparency logs. For which we could search `company.com` and find entry for domains which required a certificate.

example querying crt.sh:

get all entry for a domain:
```
curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .
```

get all unique subdomain entries:
```
curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u > subdomainlist
```

get all domains belonging to the company (we cant test domains belonging to other 3rd party):
```
for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done
```

## Shodan

Once we've created a lists of hosts that we can investigate futher we can generate a lists of IPs and run them through shodan:
```
for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done

for i in $(cat ip-addresses.txt);do shodan host $i;done
```


## DNS Records

We can use dns records to find more information about the online presence.

```
dig any $RHOST
```

This can provide us with valuable information and should be scoured for all the information it provides. We should google each bit of information, even if it seems meaningless, it will give us a much better overview of the infrastructure that may be there.

# Cloud Resources

Although cloud service providers secure their infra centrally this does not stop administrators from leaving misconfigurations and thus the resources vulnerable.

This often begins with unauthenticated access to either s3 buckets (aws), blobs (azure), cloud storage (gcp).

Cloud storage is often added to DNS when used for admin purposes by other employees. Making it easier to reach and manage them.

So... We may find cloud resources through DNS but we can also use other methods.

## Google Dorks

We can use a combination of googling and google dorks to find insecure cloud storage with `inurl:` and `intext:`.

example:
```
intext: <company_name> inurl:amazonaws.com

intext: <company_name> inurl:blob.core.windows.net
```

As part of these results we may receive PDF, Documents and other file types. This is because these files are most likely hosted within cloud storage and are linked to the page in the source code.

Third party providers like `domain.glass` can also tell us a lot about the company infra. As well as some security measures that may be in place like cloudflare.

Another useful provider being `GrayHatWarfare`. Which can be used to further search cloud storages. Giving us further control, for example, to filter by file type. This could lead us to discovering exposed ssh keys.


# Staff

Searching for and identifying staff on social media platforms can also reveal a lot about the teams infra and makeup. This could reveal the tech being used, including applications and programming languages.

Targeting individuals can reveal the duties on the job that they have, and tell us a lot about what we may not be able to see. This can be gauged by what they share with others and the information they have on their profile.

We can also utilize linkedin job postings which may also reveal a lot. Including tech stacks and what software may be utilized by a company.

From this recon we may find an employees personal github, which may reveal sensitive information, such as emails, passwords, and hardcoded tokens. This can be accelerated by the research of common misconfigurations for the framework or stack that is in use within the repository. `We may be able to find a dedicated OWASP Top 10 repository for particular framworks and technologies`. (e.g. https://github.com/boomcamp/django-security)

