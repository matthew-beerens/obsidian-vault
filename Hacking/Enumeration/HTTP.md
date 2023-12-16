
Commands
=
`whatweb IP` - web technologies
`http IP` - http request to website, header information, redirects
`dirb http://IP `- enumerate directories, is recursive
`browsh --startup-url URL/dir` - browse website from command line
`curl` - cli web request
`wget` -retrieve web files
`lynx url `- like browsh

nmap scripts
=
`--scrip`t
	`http-enum` - find interesting folders etc
	`http-headers `- website header information (cookies, tech, protections, etc.)
	`http-methods --script-args http.methods.url-path=/specific path to check/` - get methods of paticular paths - CHECK RISKY METHODS
	`http-webdav-scan` - webDAV installations, set of extensions for http, create change move documents on a server
	`banner`

msfconsole
=
`auxiliary/scanner/http/*`
	`http_version`
	`brute_dirs` - directory enumeration
	`robots_txt`

Helpful
=
check: 
robots.txt
sitemap.xml