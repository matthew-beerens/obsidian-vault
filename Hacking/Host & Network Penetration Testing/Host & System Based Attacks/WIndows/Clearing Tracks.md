
must be conducted in a legitimate penetration test to remove artifacts, reverse shells and what not

store everything in C://temp so it can be cleared after pentest

keep note of and make use of rc scripts generated by msfconsole to remove artifacts after pentest is done (also the paths if you wanna do it manually)

### meterpreter
1. `clearev` - clear event log in windows
2. `resource /path/to/rc` run resource script to delete changes