We started with a port scan using Nmap:

![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/LazyAdmin/Images/nmap.png?raw=true)

Open Ports:
- 22 SSH
- 80 HTTP

We began by fuzzing the website and discovered the directory /content.
Fuzzing further from the discovered directory, we found multiple subdirectories.

![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/LazyAdmin/Images/fuzz1.png?raw=true)

![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/LazyAdmin/Images/fuzz2.png?raw=true)

There is a login form at http://10.10.244.177/content/as/

There is also an interesting list of files available at http://10.10.244.177/content/inc/

The admin of the website has allowed directory listing when it should not be allowed.

The most interesting file is mysql_backup_20191129023059-1.5.1.sql

We can download the file directly using the command :
```
wget http://10.10.244.177/content/inc/mysql_backup/mysql_backup_20191129023059-1.5.1.sql
```
Upon inspection, we found a hash and a username:

- Hash: 42f749ade7f9e195bf475f37a44cafcb
- Username: manager

![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/LazyAdmin/Images/creds-file.png?raw=true)

We can decrypt the hash using CrackStation. It’s the MD5 hash of the password Password123.

Using these credentials, we can log in at http://10.10.244.177/content/as/

Next, we want to upload a reverse shell to execute commands on the target system.

We can upload the PHP reverse shell from https://github.com/pentestmonkey/php-reverse-shell/ here : 

![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/LazyAdmin/Images/revshell.png?raw=true)

We start our listener on our local machine with :

```
nc -lvnp 1234
```

We can trigger our reverse shell by navigating to http://10.10.244.177/content/inc/ads/ and clicking on our uploaded file.

To obtain the user flag, go to /home/itguy and run :
```
cat user.txt
```
![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/LazyAdmin/Images/user-flag.png?raw=true)


Next, we run sudo -l, which shows that we can run /home/itguy/backup.pl as root.

This file is essentially running /etc/copy.sh.

![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/LazyAdmin/Images/sudoL.png?raw=true)


We check the permissions of /etc/copy.sh and see that we have write access to the file.

All we need to do is add bash -p (which spawns a bash session in privileged mode) to /etc/copy.sh. 

This command will be executed as root when we run :
```
sudo /usr/bin/perl /home/itguy/backup.pl
```
Here’s how to do it :
```
echo "bash -p" > /etc/copy.sh
```
Then execute :
```
sudo /usr/bin/perl /home/itguy/backup.pl
```
Now, we are root and can obtain the root flag.

```
cat /root/*
```
![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/LazyAdmin/Images/root-flag.png?raw=true)


VOILA ! 
