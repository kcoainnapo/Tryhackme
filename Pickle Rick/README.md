Nmap Scan :
```
nmap 10.10.73.60 -p- -sV -sC  > nmap.txt
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Rick is sup4r cool
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/main.png?raw=true)

Viewing the page source at http://10.10.73.60/ reveals the username: R1ckRul3s

We tried fuzzing with : 
```
ffuf -u http://10.10.73.60/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -c
```
Nothing interesting ! So we tried :
```
ffuf -u http://10.10.73.60/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-large-files.txt -c
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/Fuzz.png?raw=true)

The robots.txt file contains: Wubbalubbadubdub

Visiting login.php brings up a login portal : 

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/login.png?raw=true)

We can use the following credentials R1ckRul3s:Wubbalubbadubdub

After logging in, we can execute commands :

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/logged_in.png?raw=true)

However the famous cat command is not allowed as well as the head and tail commands :

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/execute.png?raw=true)

But we can bypass this restriction with the strings command, giving us the first ingredient :

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/strings-bypass.png?raw=true)

After exploring further, we found the second ingredient :

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/2nd-ingredient.png?raw=true)

We try to check our privileges with the command :
```
sudo -l
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/sudo.png?raw=true)

BINGO ! 

We can explore the root directory using sudo :

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/root-directory.png?raw=true)

We can now retrieve the last ingredient using :
```
sudo strings /root/3rd.txt
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/3rd-ingredient.png?raw=true)

VOILA !




