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

we get this message after nagivating to the web page

```
Dear agents,

Use your own codename as user-agent to access the site.

From,
Agent R 
```

user-agent is an option we can set using curl

```
kali@kali:~$ curl -A "R" -L 10.10.206.40
What are you doing! Are you one of the 25 employees? If not, I going to report this incident
<!DocType html>
<html>
<head>
        <title>Annoucement</title>
</head>

<body>
<p>
        Dear agents,
        <br><br>
        Use your own <b>codename</b> as user-agent to access the site.
        <br><br>
        From,<br>
        Agent R
</p>
</body>
</html>
```

answer
```
user-agent
```

### What is the agent name?

we get this message when we set -A to "C"

```
kali@kali:~$ curl -A "C" -L 10.10.206.40
Attention chris, <br><br>

Do you still remember our deal? Please tell agent J about the stuff ASAP. Also, change your god damn password, is weak! <br><br>

From,<br>
Agent R 
```

answer
```
chris
```

## Task 3

### FTP password

lets crack chris' FTP password using hydra

