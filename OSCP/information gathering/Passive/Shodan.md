Shodan is a search engine that crawls devices connected to the internet including:
- servers
- routers
- IoT devices
- Other internet-connected devices

shodan interacts with these devices and displays information about them.

### Useful search queries

`hostname:<url>` - returns information containing a list of IPs, services and banner information of the target search. This information gives us a snapshot of our targets internet footprint. clicking on an IP address in the results will return a summary of the host. the returned information may reveal vulnerable technologies.