# Neighbour — TryHackMe

**Date:** June 2026  
**Difficulty:** Easy  
**Category:** Web, Authentication Bypass  
**Status:** ✅ Completed

---

# 🔍 Summary

Neighbour is a beginner web challenge focused on insecure direct object references (IDOR). By inspecting the source code and manipulating URL parameters, it is possible to access another user's profile and retrieve the flag.

---

# 🛠️ Tools Used

- Nmap
- Browser
- View Source

---

# 📡 Reconnaissance

## Nmap Scan

```bash
nmap -sS -A -T4 MACHINE_IP
```

<img width="830" height="743" alt="Screenshot 2026-06-03 211333" src="https://github.com/user-attachments/assets/9955406e-b9c6-41a7-a1fc-3c07654badd3" />


### Open Ports

| Port | Service |
|--------|---------|
| 22 | SSH |
| 80 | HTTP |

The web application presented a login page.

<img width="2361" height="933" alt="Screenshot 2026-06-03 211415" src="https://github.com/user-attachments/assets/f4d8d469-f0c5-477b-9700-868c0689643f" />


---

# 🔍 Source Code Review

Viewing the page source revealed an important developer comment:

```html
<!-- use guest:guest credentials until registration is fixed.
"admin" user account is off limits!!!!! -->
```

<img width="996" height="770" alt="Screenshot 2026-06-03 211443" src="https://github.com/user-attachments/assets/88f10741-3690-48fe-9da8-9960f339f970" />


This provided valid guest credentials.

---

# 🔓 Initial Access

Logged in using:

```text
Username: guest
Password: guest
```

<img width="2364" height="549" alt="Screenshot 2026-06-03 211521" src="https://github.com/user-attachments/assets/a829d33b-7042-4012-bfe1-0ea24228503d" />


After authentication the URL became:

```text
/profile.php?user=guest
```

---

# 🚩 Exploitation

The application used the username directly inside the URL parameter.

Original URL:

```text
/profile.php?user=guest
```

Changing the parameter to:

```text
/profile.php?user=admin
```

allowed access to the administrator profile.

This is a classic **IDOR (Insecure Direct Object Reference)** vulnerability.

---

# 🏁 Flag

After changing the parameter to `admin`, the flag was displayed directly on the page.

<img width="2366" height="408" alt="Screenshot 2026-06-03 211537" src="https://github.com/user-attachments/assets/54a5ba96-50e8-4ee2-8e9b-8e36f850f473" />


```text
flag{66be95c478473d91a5358f2440c7af1f}
```

---
<img width="1614" height="864" alt="Screenshot 2026-06-03 211614" src="https://github.com/user-attachments/assets/63126c3d-105c-41c3-9d3b-b9dd2a7dcde5" />

# 📚 Lessons Learned

- Source code comments can reveal sensitive information.
- Never trust client-side restrictions.
- User-controlled identifiers should always be validated server-side.
- IDOR vulnerabilities can allow unauthorized access to other users' data.
- URL parameters should never be used as the sole authorization mechanism.

---
