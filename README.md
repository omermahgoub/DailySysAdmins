# Linux

### TCPdump tips and tricks 
A practical tcpdump guide to lift your troubleshooting skills. This is not only a list of command and tips but a complete guide to use tcpdump and master it. Tcpdump is an essential tool for all SysAdmin.

#### Tcpdump Command Line
The following command uses common parameters
```
sudo tcpdump -i eth0 -nn -s0 -v port 80
```
`-i` : Select interface that the capture is to take place on, this will often be an *ethernet card* for sysadmin but could also be a *vlan* or something more unusual. Not always required if there is only one network adapter.

`-nn` : A single (n) will *not resolve hostnames*. A double (nn) will *not resolve hostnames or ports*. This is handy for not only viewing the IP / port numbers but also when capturing a large amount of data, as the name resolution will slow down the capture.

`-s0` : Snap length, is the size of the packet to capture. -s0 will *set the size to unlimited* - use this if you want to capture all the traffic. Needed if you want to pull binaries / files from network traffic.

`-v` : Verbose, using (-v) or (-vv) *increases the amount of detail shown* in the output, often showing more protocol specific information.

*port 80* : this is a common port filter to capture only traffic on port 80, that is of course usually HTTP.
