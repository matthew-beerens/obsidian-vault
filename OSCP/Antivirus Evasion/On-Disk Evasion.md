
Highly effective antivirus evasion requires a combination of all of the follow plus more advanced techniques including anti-reversing, anti-debugging, virtual machine emulation detection and more.

The Enigma Protector can be used to successfully bypass antivirus products.

#### Packers

have been used in the past to evade antivirus 
- packers make an executable that is smaller than the original but also equivalent to the original source 
- this creates a new hash signature and therefore could bypass signature based detection on disk 
- this is not enough for modern AV scanners.

#### Obfuscators

- reorganize and mutate code
- harder to reverse engineer - 
	- replacing instructions with semantic equivalents
	- inserting irrelevant instructions
	- splitting/reorganizing functions.
- marginally effective against signature based AV detections
- modern obfuscators have runtime in-memory capabilities - hindering AV detection further

#### Crypter Software

- cryptographically alter executable code. 
- decryption stub restores code at execution time. this happens in memory. 
- only the encrypted code is on disk. 
- Foundational in modern malware. 
- One of the most effective AV evasion techniques.


