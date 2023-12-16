command line wireshark

better for looking at something specific, good for automating analysis tasks running tools into it

Commands
=
`tshark -D` - interfaces
`tshark -i etho0` - listen on interface
`tshark -r pcap` read pcap file with tshark 
`tshark -r pcap -z io,phs -q` - packet hierarchy

Filters
=
`-Y 'http'` - filter by http packets
`-Y 'ip.src==IP_ADDRESS && ip.dst==IP_ADDRESS'` - filter by src and destination ip
`-Y 'http.request.method==GET -Tfields -e frame.time -e ip.src -e http.request.full_uri'` - filter by http get  and get specific fields
`-Y 'http contains password'` - search for packets containing passwords
`-Y 'http.request.method==get && http.host==URL' -Tfields -e ip.dst` - specific website
`-Y 'ip contains websitename && ip.src==IPADDR' -tFields -e ip.src -e http.cookie` - find all cookies for a specific website with specific ip add
`-Y 'ip.src==IPADDR && http' -tFields -e http.user_agent` - what browser is being used