
## Running a successful command

going into the network tab of a web browser and copying the request as a cURL command pasting it into the terminal and replacing curl with sqlmap is an easy way to run a successful command on an endpoint.

you can save a request from burpsuite of zap and use it in sqlmap with -r, you can also copy the request headers the same way we copy as cURL
## commands

`sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'` simple sqlmap command
	* - adding an astering to the value of the field you want to test will narrow down the injection testing
`sqlmap -r request.txt` - command with saved request form burp

## Tuning

- `sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"` using a prefix of suffix that may not be covered by sqlmap if you discover weird code on the backend
- `--level 1-5` - extends vectors and boundaries
- `--risk 1-3` - increase risk of dos or entry loss
- `-v 3` - will show all payloads being used
- `--code 200` - can be used to signify a positive result from http request when testing injections if sqlmap is not picking up correct responses or differences in repsonses
- `--titles` can be used to mark the differences in responses if noticed through titles if sqlmap did not pick up the positive responses to injections
- `--string=success` - also a marker for difference in http response
- `--text-only` - basis comparison of difference using only the text in a html document, removes html tags
- `--technique=BEU` - sometimes we need to narrow down techniques and payloads due to timeouts and other issues
- `--union-cols=num` - sometimes union testing requires extra information, if we can manullay find the number of tables in a db, we can let sqlmap know
- `--union-char='a'` incase null and random integers arnt working for junk colums we can let sqlmap know a valid data type and value to use instead
- `--union-from=users` if needed we can specify the table to pull from in union testing as sometimes sqlmap fails to do this automaticaaly due  to inability to detect the dbms name
## Flags

- ` --random-agent` - important switch as alot of security systems will filter out sqlmaps user agent.
- `--method` - select a different http method
- `--parse-errors` - when sqlmap isnt working well, use this to see more errors that my be thrown by the db
- `-t output.txt` stores all traffic  to a file for analysis of whats going wrong
- `-v 6` - set lvl of verbosity 
- `--proxy` send all requests through a proxy like burp to analyse it

## TIPS

if the parameter is in the header, add a * to the value before saving the request in burp  so that sqlmap can recognise it.

JSON is also an attack surface for injection, dont snub it.