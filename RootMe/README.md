We started with a port scan using nmap :
```
nmap 10.10.188.206 -p-
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/RootMe/Images/nmap.png?raw=true)

We see there is 2 ports open : 
- 80 http
- 22 ssh

What version of Apache is running?

Apache 2.4.29
```
curl http://10.10.188.206 -I
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/RootMe/Images/apache-version.png?raw=true)

We fuzz the website using ffuf and find the directory /panel that allow to upload files as well as the /uploads directory where files are registered once they are succesfully uploaded :

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/RootMe/Images/fuzz.png?raw=true)

Now we want to upload a file that will give us our initial access on the target system to be able to execute commands.

We copy the content of the php reverse shell in : https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php in a file called rev.php

Once we tried to upload it in http://10.10.188.206/panel/ we have the following error telling us that php is not allowed : 

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/RootMe/Images/php-error.png?raw=true)

After some research we find that it exists different php extension : 

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/RootMe/Images/php-extension.png?raw=true)

We were able to upload our file when we renamed it rev.php3 but once we clicked on it we had an error message.

But once we renamed it rev.php5 we were able to obtain our intial access, we started a netcat listener with :

```
nc -lvnp 1234
```

We click on our uploaded file "rev.php5" in http://10.10.188.206/uploads/ and we obtain our intial access :

![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/RootMe/Images/initial-access.png?raw=true)

We can find the user.txt file with this command : 


We enumerate the system to find privilege escalation vectors, we use the following command to find SUID binary : 
```
find / -perm -04000 2>/dev/null
```
And we came accross : 
```
/usr/bin/python
```
We can go to https://gtfobins.github.io/gtfobins/python/ and use the following command to elevate our privileges and obtain the roor flag : 
```
/usr/bin/python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```
![Image Alt](https://github.com/kcoainnapo/Tryhackme/blob/main/RootMe/Images/root-flag.png?raw=true)


