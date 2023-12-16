some obfuscators turn the code into a dictionary of all the words and symbols within the code and then attempt to rebuild during execution, can have a hit to performance.

obfuscation can be used for evading intrusion detection as well.

### Packing

http://beautifytools.com/javascript-obfuscator.php

packing is a method of obfuscation

A `packer` obfuscation tool usually attempts to convert all words and symbols of the code into a list or a dictionary and then refer to them using the `(p,a,c,k,e,d)` function to re-build the original code during execution

not completely reliable, strings may still be in plain text

 Another way of `unpacking` such code is to find the `return` value at the end and use `console.log` to print it instead of executing it

### Advances obfuscation

https://obfuscator.io/
http://www.jsfuck.com/
https://utf-8.jp/public/jjencode.html
https://utf-8.jp/public/aaencode.html

### Deobfuscators

formatters:
https://prettier.io/playground/
https://beautifier.io/

Deobfuscators:
http://www.jsnice.org/ - uncheck infer types to reduce comment clutter

### Encoding/Decoding

https://www.boxentriq.com/code-breaking/cipher-identifier  - cipher identifier

base64
`echo blashblash | base64`
	-d to decode

Hex
`echo basdalskdjfs | xxd -p`
	-p -r  to decode

rot13
`echo https://www.hackthebox.eu/ | tr 'A-Za-z' 'N-ZA-Mn-za-m'`
`echo uggcf://jjj.unpxgurobk.rh/ | tr 'A-Za-z' 'N-ZA-Mn-za-m'`

### Action

once we find and deobfuscate js code, we may be able to replicate its functionality and see if the server side is responding to it (if applicable) which may open up attack vectors, especially if this code is not yet used or released.

