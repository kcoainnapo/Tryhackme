Nmap Scan :
```
nmap 10.10.73.60 -p- -sV -sC  > nmap.txt
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Rick is sup4r cool
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/main.png?raw=true)

view-source:http://10.10.73.60/ ==> Username: R1ckRul3s

We tried to fuzz with :

```
ffuf -u http://10.10.73.60/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -c
```
Nothing interesting ! So we tried :
```
ffuf -u http://10.10.73.60/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-large-files.txt -c
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/Fuzz.png?raw=true)

robots.txt ==> Wubbalubbadubdub

Visiting login.php gives us a login portal : 

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/login.png?raw=true)

We can use the following credentials R1ckRul3s:Wubbalubbadubdub

We are logged in and can execute commands : 

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/logged_in.png?raw=true)

However the famous cat command is not allowed as well as the head and tail commands :

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/execute.png?raw=true)

But we can bypass this restriction with the strings command, giving us the first ingredient :

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/strings-bypass.png?raw=true)

By looking and playing around we obtain the second ingredient : 

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/2nd-ingredient.png?raw=true)

we try to check our privileges with the command :
```
sudo -l
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/sudo.png?raw=true)

BINGO ! 

We can look into the root directory using sudo :

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/root-directory.png?raw=true)

We can now obtain the last ingredient with : 
```
sudo strings /root/3rd.txt
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/Pickle%20Rick/Images/3rd-ingredient.png?raw=true)

VOILA !




