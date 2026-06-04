# The Game — TryHackMe

**Date:** June 2026  
**Difficulty:** Easy  
**Category:** Reverse Engineering, Static Analysis  
**Status:** ✅ Completed

---

# 🔍 Summary

The Game is a beginner reverse engineering challenge that requires inspecting a provided executable file to recover a hidden flag. By extracting the contents of the archive and analyzing strings within the executable, the flag can be reconstructed directly without executing the binary.

---

# 🛠️ Tools Used

- unzip
- strings
- grep
- Linux Terminal

---

# 📡 Reconnaissance

A ZIP archive containing the executable was provided.

```bash
unzip Tetrix.exe-1741979048280.zip
```
<img width="745" height="471" alt="Screenshot 2026-06-04 002512" src="https://github.com/user-attachments/assets/6f8c4487-2b85-4bb9-b807-ffe0d442f68f" />


The archive extracted a Windows executable named:

```text
Tetrix.exe
```

---

# 🔍 Static Analysis

Rather than executing the binary, strings were extracted and filtered for TryHackMe flag fragments.

```bash
strings Tetrix.exe | grep "THM"
```

The output revealed multiple flag fragments embedded inside the executable:

```text
FEATURE_ARITHMETIC
ATTENUATION_LOGARITHMIC
PATHFINDING_ALGORITHM_ASTAR
-4PTHM
THM{I_CAN_READ_IT_ALL}
```

The final line contained the complete flag.

---

# 🏁 Flag

The flag was recovered directly from the executable strings.


```text
THM{I_CAN_READ_IT_ALL}
```

---
<img width="1591" height="829" alt="Screenshot 2026-06-04 002546" src="https://github.com/user-attachments/assets/78bb073f-c672-4f76-935f-d90d64e8ae58" />

# 📚 Lessons Learned

- Static analysis is often sufficient for beginner reverse engineering challenges.
- The `strings` utility can quickly reveal sensitive information embedded inside binaries.
- Always inspect executables before executing them.
- Developers should never hardcode secrets or flags directly into compiled applications.
- Simple string extraction can sometimes completely bypass intended challenge logic.

---
