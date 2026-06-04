# Corridor — TryHackMe

**Date:** June 2026  
**Difficulty:** Easy  
**Category:** Web, IDOR, Hash Analysis  
**Status:** ✅ Completed

---

# 🔍 Summary

Corridor is a web-based challenge that revolves around insecure direct object references (IDOR). By inspecting the source code, identifying hidden endpoints, and analyzing MD5-hashed identifiers, it is possible to enumerate valid resources and discover the page containing the flag.

---

# 🛠️ Tools Used

- Nmap
- Browser
- View Source
- Hash Identifier
- CrackStation
- MD5 Generator

---

# 📡 Reconnaissance

## Nmap Scan

```bash
nmap -sS -A -T4 MACHINE_IP<img width="867" height="417" alt="Screenshot 2026-06-04 005103" src="https://github.com/user-attachments/assets/5f3f4508-f76b-4dab-b85b-e6e48e108f4b" />

```
<img width="867" height="417" alt="Screenshot 2026-06-04 005103" src="https://github.com/user-attachments/assets/daafbf00-49ce-41c8-ade6-3d25de949ba8" />




### Open Ports

| Port | Service |
|--------|---------|
| 80 | HTTP |

Browsing to the website revealed a corridor with multiple doors.

<img width="2358" height="1315" alt="Screenshot 2026-06-04 003645" src="https://github.com/user-attachments/assets/1d143435-9e36-4d9a-b854-5f252a78a8e7" />


---

# 🔍 Source Code Analysis

Viewing the page source revealed an image map containing multiple clickable areas.

Each door pointed to a different endpoint represented by an MD5 hash.

<img width="1544" height="768" alt="Screenshot 2026-06-04 003714" src="https://github.com/user-attachments/assets/fdbd2024-ffc8-4b80-a89a-2d89e8b54743" />


Example:

```text
c4ca4238a0b923820dcc509a6f75849b
```

---

# 🔎 Hash Identification

Using Hash Identifier showed that the values were MD5 hashes.

```bash
hash-identifier c4ca4238a0b923820dcc509a6f75849b
```

<img width="774" height="1057" alt="Screenshot 2026-06-04 003809" src="https://github.com/user-attachments/assets/4405a5cc-1dcb-4cce-95e3-ae3bbf8d6630" />


---

# 🔓 Hash Cracking

All discovered hashes were submitted to CrackStation.

<img width="2369" height="1220" alt="Screenshot 2026-06-04 004445" src="https://github.com/user-attachments/assets/6584e326-6a62-4f22-892c-cf18e9897d15" />


The hashes corresponded to sequential numbers:

```text
1
2
3
4
...
13
```

This indicated that each door likely represented a numbered resource.

---

# 🚩 Exploitation

Since the discovered hashes represented numbers, additional enumeration was performed.

Numbers like **-1**, **0**, and **1** are typically unique when dealing with IDOR vulnerabilities because administrative, hidden, or test objects are often assigned these identifiers.

The MD5 hash for:

```text
0
```

was generated.

```text
cfcd208495d565ef66e7dff9f98764da
```

<img width="2358" height="418" alt="Screenshot 2026-06-04 004708" src="https://github.com/user-attachments/assets/464c3dfc-6c67-4aa3-813b-88b41cbb39ea" />


Navigating to:

```text
http://MACHINE_IP/cfcd208495d565ef66e7dff9f98764da
```

revealed the hidden page.

---

# 🏁 Flag

The flag was displayed directly on the page.

<img width="2359" height="888" alt="Screenshot 2026-06-04 004737" src="https://github.com/user-attachments/assets/cd50094e-389c-4cc1-920a-b57b79cbbe41" />


```text
flag{2477ef02448ad9156661ac40a6b8862e}
```

---
<img width="1591" height="834" alt="Screenshot 2026-06-04 004750" src="https://github.com/user-attachments/assets/5929097f-0c7e-4382-a8a7-9a00d61501db" />

# 📚 Lessons Learned

- Source code often reveals hidden application functionality.
- MD5 hashes are easily recognizable and reversible when generated from predictable values.
- Sequential identifiers frequently indicate potential IDOR vulnerabilities.
- Testing special values such as `-1`, `0`, and `1` can uncover hidden resources.
- Security through obscurity is ineffective when identifiers can be enumerated.

---
