# Full stack for Front End Engineers
- with Jem Young
- jemyoung.com/fsfe
- My clone of the slides: https://docs.google.com/presentation/d/1HyXKwp1QVWjod2y9Ng4yxVI_v4q6ZBzs1CM9rZ9cRT4/edit?usp=sharing

## Command line / VIM
### Shells
Command line interpreter

`echo $0` - $ expands

shell > application > kernal

### Terminal
Runs shell applications

### Command
#### Normies
- cd - change directory
- ls - list directory contents
- mkdir - make directory
- rm - remove file

#### More
- rmdir - remove directory
- pwd - print working directory
- cat - show file contents
- man - command manual
 - Search in man... "/searchterm"
- less - show file contents by page
- echo - repeat input
- pkill - stop process `pkill node`
- clear - :)

### VIM
#### Modes
- insert (text editing `i`)
- command (primary - `ESC`)
- last line (searching saving, exiting: `:`)

#### Last line
- `:set number` / `:set nonumber`

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

## Server
- VPS: Vitual Private Server (part of a server but isolated)
- SSH: Secure Socket Shell

### Simple server
```js
const http = require('http');

http.createServer(function (req, res) {
  res.write('Hello, World!');
  res.end();
}).listen(8080);

console.log('Server started! Listening on port 8080');
```
- Run by calling file name with `node`
- Reserved ports below 1000 usually

### Exercise VPS
- Buy VPS
- digitalocean.com
- TODO: write in base settings + OS tree
- Linux
  - Kernel + Utilities

### SSH
- Super secure, only your computer has the private key, server has public
- SSH usually on port `22` by default

#### Making one SSH
- `cd ~/.ssh`
- `ssh-keygen`
- Name it!
- Password not required but suggested
- `ls | grep fsfe`
```
fsfe // private key file
fsfe.pub // public key
```
- SSH into server `ssh root@SERVER_ID`
- In `ssh -i ~/.ssh/fsfe root@YOUR_IP`
- Exiting server `exit` :)
- `whoami` - user

## DNS Records
- A record: Maps name to IP address
  - @ and www need to map
- CNAME: maps name to name (usually with subdomains)
- `dig jemyoung.com`

## Server setup
1. Update software `apt update` , `apt upgrade`
2. Create a new user `adduser $USERNAME`
3. Make that user a super user `usermod -aG sudo $USERNAME`
4. Enable login for new user `su $USERNAME`
5. Disable root login `sudo cat /var/log/auth.log`
6. Watching your log `head` top lines of a file, `tail -f` opposite
7. Add ssh to new user `cd ~`, `mkdir -p ~/.ssh`, `vi ~/.ssh/authorized_keys` (paste public key)
8. Login from new user `exit` 2x, 
... See slides for setup

### Install Nginx (engine-x)
- An alternative Apache which is good for LAMP stack
- Reverse proxy, web server, proxy server
... See slides for setup

### Install NodeJS + Git
- Bind with v8 engine

## App setup
... See slides

## Process manager
- Keeps things running, restarts as well if errors
- PM2
... See slides













