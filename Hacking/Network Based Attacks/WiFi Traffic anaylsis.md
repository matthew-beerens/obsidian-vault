
Helpful
=
`wlan.fc.type_subtype==0x0008 && !(wlan.wfa.ie.wpa.version==1) && !(wlan.tag.number==48)` - looking for beacon frames that dont use wpa or rsn sercurity features
`wlan contains SSID` - find traffic for specific network - find channel information in DS parameters