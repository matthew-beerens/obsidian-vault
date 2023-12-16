
SNMP is the Simple Network Management Protocol. Not well understood by network administrators which can result in significant information leaks. based on UDP - stateless. 

Susceptible to IP spoofing and replay attacks.

Protocols 1, 2 and 2c offer no traffic encryption - credentials easily intercepted.

Weak authentication schemes are commonly left configured - default public and private community strings.

Commonly misconfigured and vulnerable.

### SNMP Management Information Base (MIB)

The SNMP MIB is a tree structured database with the branches representing different organizations or network functions and the leaves correspond to specific variable values that can be accessed and probed by an external user.

### Scanning SNMP

`sudo nmap -sU --open -p 161 192.168.1.1-254 -oG open-snmp.txt` - Will scan all machines on network for open SNMP port.

using onesixtyone:

1. `echo public > community`
2. `echo private >> community`
3. `echo manager >> community`
4. `for ip in $(seq 1 254); do echo 192.168.1.$ip; done > ips`
5. `onesixtyone -c community -i ips`

### SNMP Walking

Table of SNMP Microsoft specific MIB values - contain much more information than network based information:

```
1.3.6.1.2.1.25.1.6.0 - System Processes 
1.3.6.1.2.1.25.4.2.1.2 - Running Programs
1.3.6.1.2.1.25.4.2.1.4 - Processes Path
1.3.6.1.2.1.25.2.3.1.4 - Storage Units
1.3.6.1.2.1.25.6.3.1.2 - Software Name (all installed software)
1.3.6.1.4.1.77.1.2.25 - User Accounts
1.3.6.1.2.1.6.13.1.3 - TCP Local Ports 
```

Once we find SNMP services on the target we can query for specific MIB data.
To do this we must know the SNMP read-only community string which in most cases is `"public"`.

`snmpwalk -c public -v1 -t 10 <ip>` - This snmpwalk can be used to enumerate an exposed SNMP service. `-c` specifies the community string, `-v` is the version of SNMP, `-t` specifies the timeout in seconds. The output may also reveal other information such as email addresses which could be further used for social engineering.

`snmpwalk -c public -v1 -t 10 <ip> <OID_FROM_TABLE>` - we can snmpwalk with parsed OID to find specific network information, including open tcp port that may not have shown in preliminary scanning.

`-Oa` can be used to automatically transcribe hexadecimal values to ASCII - might as well use it.



