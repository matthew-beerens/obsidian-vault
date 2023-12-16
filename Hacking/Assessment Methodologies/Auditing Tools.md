
Guides and Tools for Standardization Across Organizations
=
SCAP (Security Content Automation Protocol) tool
STIGs (Security Technical Implementation Guides)

SCAP Tool
=
- compliance checker
- scans machine for compliance of framework/standards using a checklist
- will provide a compliance score
	- right click on scan and view in browser for these details
- results show where things fails and why they failed - why its important

STIG Viewer
=
Used to view the results of the scan made with SCAP Tool

We can use this tool to look at issues and remediation of the compliance failures

Categorized breakdown of STIGs:
1. about how failure to comply leads to exploitations resulting in loss of CIA
2. potential loss of CIA
3. degrades measures to counter loss of CIA

LGPO
=
command line tool, can be used to implement policy

usage:
LGPO.exe /g {POLICY}

Nmap for Asset Management
=
use nmap to check for accurate policy compliance of machines in the network
good for on the fly asset management

Nessus
=
use nessus for general vulnerability scanning assessment, scan a range of IPs
use this information to further audit network/end devices
can be used to generate a report of audit