
Startup
=
1. service postgresql start
2. msfconsole

Helpful
=
`show all`
`show exploits`
`search cve:YEAR type:exploit platform:windows`
`connect IP PORT` - like netcat but msf

Workspaces
=
`workspace` - current workspace
`hosts` - current hosts in workspace
`workspace -a newworkspace_name` - create new workspace
`workspace -d name` - delete workspace

Import nmap scan
=
`db_import /path/to/xml/nmap`
`hosts` - confirm import worked
`services` prints services of hosts from import
`vulns` - shows vulnerabilities found from scans
`creds` - credentials found
`loot` - files useful that were found


MSF Nmap Scan
=
`db_nmap flags target_ip` automatically imports nmap scans, msf conducts scan