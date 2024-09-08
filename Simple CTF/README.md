We start with a port scan using nmap : 

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Simple%20CTF/Images/nmap.png?raw=true)

We discover 3 open ports : 
- 21 FTP
- 80 HTTP
- 2222 SSH

We connect to FTP using the command : 
```
ftp 10.10.199.250
```
Once connected we can use the command "ls" to see the content

If after running "ls" and you are prompted witht the message : 229 Entering Extended Passive Mode (|||40671|)

Press CTRL + c and enter "passive" you will then be able to navigate the FTP server 

we download the file called "ForMitch.txt" with the command : 
```
get ForMitch.txt
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Simple%20CTF/Images/ftp.png?raw=true)


It tells us that some password has been reused and it's easily crackable.

When we navigate to http://10.10.199.250 we see that : 

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Simple%20CTF/Images/apache-default.png?raw=true)


This page appears when the Apache2 web server is successfully installed and running on an Ubuntu system. It serves as the default test page to confirm that the server is working

We start fuzzing the website to discover juicy content and come across /simple

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Simple%20CTF/Images/fuzz.png?raw=true)


Scrolling down the page http://10.10.199.250/simple/ we find the version of the CMS running : 2.2.8

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Simple%20CTF/Images/CMS-version.png?raw=true)


We can search for exploits regarding this particular version using searchsploit : 

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Simple%20CTF/Images/get-exploit.png?raw=true)


It looks like the version of the CMS running is vulnerable to SQL Injection.

The exploit is written in python2, if trying to run the exploit you have the error message "termcolor module error" install it for Python 2 using :
```
pip2 install termcolor
```
The command to run the exploit is :
```
python2 46635.py -u http://10.10.199.250/simple/ -w /usr/share/seclists/Passwords/Common-Credentials/best110.txt --crack
```
If you don't specify the --crack option you will have to crack the password manually.

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Simple%20CTF/Images/exploit-result.png?raw=true)

To crack the password manually, you have to create a file with this hash + salt format : hash:salt 

And use the command : 
```
hashcat -m 20 -a 0 hash.txt /path/to/wordlist.txt
```
the -m 20 option specifies an MD5 hash with a salt

We can then connect to the SSH service on port 2222 and obtain the user flag :

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Simple%20CTF/Images/ssh.png?raw=true)

Running sudo -l reveals that we can run Vim as root, so we check https://gtfobins.github.io/gtfobins/vim/

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Simple%20CTF/Images/sudo-vim.png?raw=true)

We ran : 
```
sudo vim -c ':!/bin/sh'
```
We are now root and can obtain the root flag :
```
cat /root/*
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Simple%20CTF/Images/root.png?raw=true)

VOILA ! 




