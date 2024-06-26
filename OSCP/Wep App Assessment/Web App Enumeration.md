

### Debugging Page Content

We can use the debug tool in the developer tools of our web browser to debug web applications.

In the debug tool we can view and modify web application code.

We can also use the inspector tool to closer inspect web page source.


### Inspecting HTTP Response Headers and Sitemaps

Inspecting the HTTP response headers can reveal helpful information, such as web server information, the service running and version running. It can also reveal security misconfiguration that may be present. They can give an idea of the security posture of the team maintaining the web application.

Headers are not always generated by the application an can be also generated by an intermediary proxy. e.g. `X-Forwarded-For` - signals the webserver about the original client IP address.

Sitemaps are used to help bots crawl and index their website. This also includes what not to crawl. This can reveal sensitive pages we might be interested in adding to our attack surface.


### Enumerating and Abusing APIs

Many web applications are internally built and closed source shipped with a number of APIs which provide a solid backbone of functions. a lot of these APIs are REST APIs.

we can web directly enumeration technique to brute-force rest APIs . We can do this using the pattern feature of Gobuster.

1. create a pattern file like such and save as `pattern`:
	`{GOBUSTER}/v1`
	`{GOBUSTER}/v2`
2. make use of pattern file within command using `-p`
	`gobuster dir -u <url> -w <wordlist> -p pattern`

This technique will brute-force using the dictionary list with the addition of a version, which is typical structure for a rest API. With this method we may find an API endpoint

`browsing to the /ui path may present us with the full API's documentation, this is common in white box pentest but not so much in black box`

`curl -i <url>/endpoint` - We can further inspect the API endpoint through use of curl.

we may find extra information which we can also tac onto our endpoint and enumerate further. for example with we found a username list we could add the username to our endpoint and further brute-force with gobuster.

Repeat steps until no longer able to.

`curl -d '{"field":"value", "field":"value"}' -H 'Content-Type:  application/json' <url>/endpoint` - This curl command can be used in situations where we may need to send json data to an endpoint, where we may need to attempt to login or change a password or register a new user.

`We may be able to abuse user creation features by attempting to flip an admin switch to true on user signup with json`

The `-X 'METHOD'` parameter of curl and be used to define the method used during a curl web request. 

`-H 'Authorization: OAuth <JWT_token>'` - can be passed when we were able to successfully create a login session with curl

`--proxy 127.0.0.1:8080` - can be passed to curl to send the request through burp suite first in order to make use of burp instead. all the above steps can be repeated with burp.

We can track all of our endpoints through burp suites site map under Target>Site map