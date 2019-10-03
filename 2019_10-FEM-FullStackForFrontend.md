# Full stack for Front End Engineers
- with Jem Young
- jemyoung.com/fsfe
## Command line
### Normies
- cd - change directory
- ls - list directory contents
- mkdir - make directory
- rm - remove file

### More
- rmdir - remove directory
- pwd - print working directory
- cat - show file contents
- man - command manual
 - Search in man... "/searchterm"
- less - show file contents by page
- echo - repeat input
- pkill - stop process `pkill node`
- clear - :)

#### Shells
Command line interpreter

`echo $0` - $ expands

#### Terminal
Runs shell applications

shell > application > kernal

## How does the internet work
- IPv4 (8.8.8.8) to IPv6 (2001:4860:1000:8888)
- TCP: Transmission Control Protocol (contracts and then send packets and error corrects if missing packets)
- UDP: User Datagram Protocol (just sends data doesn't care if requester gets it or listening)
- TLD: top level domain (.com etc)
- Nameserver: Map domains to IP addresses
- ICMP: Internet Control Message Protocol (ping + traceroute)
  - Option to turn those off

### Exercise Ping
- `ping twitter.com`
- good for debugging

### Exercise Traceroute
- `man traceroute`
- `traceroute google.com`
- `traceroute frontendmasters.com`
  - Does not respond to ICMP on purpose




