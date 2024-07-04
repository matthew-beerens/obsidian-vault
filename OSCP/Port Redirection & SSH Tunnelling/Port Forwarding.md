
once we exploit a target we should enumerate for other interface we can pivot to.

`ip addr` - check which other network interfaces are active on the system

`ip route` - check the routes on the system to see which subnets we can access and through which interface

At this point enumeration would take place if it hasn't been done already - check for files containing sensitive information, like config files, and other known sources that could be useful for finding information to pivot.

Once we find information that could lead us to a pivot, and a location to pivot to - we may not be able to interact with it on the current system. For example if there is an internal PostgreSQL database, but the system were on doesn't have a PostgreSQL client, we will need to port forward a port on the wan our kali machines can connect to so we can use our own local client to interact with the server.

We may need to transfer tools to the target machine to achieve this if one is not already present - such as socat.


`socat -ddd TCP-LISTEN:<listening/forward port>,fork TCP:<target_ip>:<target_port>` - set up a port forward from a port we connect to, to a port and address we cant connect to.

Afterward we can then connect to the end device service we have forwarded to - if we are using tools from our kali machine we will just need to make sure we can define a target ip and a target port.

`netcat and FIFO named pipe can also be used to create a portforward, as well as ip tables configuration or rinetd`

Test out ligolo-ng

