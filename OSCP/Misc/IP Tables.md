
We can set up IP tables to monitor the amount of traffic coming in and out of our host.

1. `sudo iptables -I INPUT 1 -s <source_ip> -j ACCEPT`
2. `sudo iptables -I OUTPUT 1 -d <dest_ip> -j ACCEPT`
3. `sudo iptables -Z`
4. `sudo iptables -vn -L`

