https://tryhackme.com/room/agentsudoctf

## Task 1

no answer required

## Task 2

### How many open ports?

```
kali@kali:~/Downloads$ sudo masscan -e tun0 -p1-65535,U:1-65535 10.10.164.129 --rate=1000
[sudo] password for kali: 

Starting masscan 1.0.5 (http://bit.ly/14GZzcT) at 2020-07-26 15:04:13 GMT
 -- forced options: -sS -Pn -n --randomize-hosts -v --send-eth
Initiating SYN Stealth Scan
Scanning 1 hosts [131070 ports/host]
Discovered open port 21/tcp on 10.10.164.129                                   
Discovered open port 80/tcp on 10.10.164.129                                   
Discovered open port 22/tcp on 10.10.164.129   
```
answer

```
3
```

### How you redirect yourself to a secret page?

