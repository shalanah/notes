# Full stack for Front End Engineers
- with Jem Young
- jemyoung.com/fsfe
- jemyoung.com/fsfe2
- TODO: Done with course? Dup slides

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

#### Install NodeJS + Git
- Bind with v8 engine

### App setup
... See slides

### Process manager
- Keeps things running, restarts as well if errors
- PM2
... See slides

### Set up SSH on server and link with repo

## Standard Streams
(unix)
- standard output `stdout`
- standard input `stdin`
- standard error `stderr`

### Redirections
- logging in `/var/log`
- `|`  read from stdout
- `>`  write stdout to file
- `>>` append stdout to file
- `<` read from stdin
- `2>` read from stderr

### Finding things
- `find`, `grep`
- `find /bar -name foo.txt` `find ${dir} ${option} ${file/folder}`
- good options `-`: name, type, empty, executable, writable
- Find log files `find /var/log -type f -name  "*.log"`
- Find all log files `find / -type d -name log`
- auth log `cat /var/log/auth.log`
- `grep -i 'jem' /var/www` `grep ${options} `
- `zgrep FILE` search inside gzip file
- Search for me in log `sudo cat /var/log/auth.log | grep shalanah`

## Nginx
### Redirect / gzip
- 301 Permanent
- 302 Temporary redirect
- gzipping on by default! yay!
### Security
- Prevent zero days `sudo apt install unattended-upgrades`
- Settings `cat /etc/apt/apt.conf.d/50unattended-upgrades
`

## Security
- SSH
- Firewalls
- Updates
- Two factor authentication
- VPN

### Ports
- Install nmap and figure out what ports are running on our ip (or other ips)
- Port defaults `less /etc/services`

### Firewall
- ufw - uncomplicated firewall
- closing port 3000 because it is not need
- `sudo ufw enable`
- `sudo ufw status`
- `sudo ufw status verbose`
- `sudo ufw allow ssh`
- `sudo ufw allow http`

### Permissions
- `ls -la`

## Cool links
- explainshell.com

## HTTP
### Headers
- Custom headers start with `X-`
### Response statuses
- 1xx Information
- 2xx Success
- 3xx Redirect
- 4xx Client error
- 5xx Server error

## HTTPS with certbot
- give it a server name
- `sudo vi /etc/nginx/sites-available/default `
- `server_name` `nicetwitter.com www.nicetwitter.com`
- open firewall `sudo ufw allow https`

## HTTP/2
- Needs https (SSL)
- Allows multiplexing
  - One connection multiple things over it
- `sudo vi /etc/nginx/sites-available/default`
- `listen 443 http2`
- `sudo service nginx reload`
- https://tools.keycdn.com/http2-test

## HTTP/3
- TCP is for HTTP prior to HTTP/3
- HTTP/3 uses UDP (Quick UDP: QUDP)

## Containers
- Not a full OS but only the libraries that are needed
- Talks to the OS for you can have lots of containers on any OS
- Orchestration Layers: deploying lots of containers
  - Ex: Kubernetes "K8s"
- Load Balancers: Routes traffic based on usage and server availability (splits evenly
- Deployments: Ansible, Vagrant, Puppet

## Looking at our load
- `top` (is heavy to use... don't keep running)
- `htop`: `sudo apt install htop`

## Databases
- Simplest way of saving persistent data is a file
  - Not great for multiples servers to open and save and write at many times
- Relational
  - SQL
  - Tables
  - Related data
  - Strict structure
- Non-relational / NoSql
  - Data agnostic
  - Loose structure

## Websocket
- Modify `sudo vi /etc/nginx/sites-available/default`
















