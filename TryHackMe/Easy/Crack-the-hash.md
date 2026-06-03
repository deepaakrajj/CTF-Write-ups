# Crack the Hash — TryHackMe

**Date:** June 2026  
**Difficulty:** Easy  
**Category:** Cryptography, Password Cracking  
**Status:** ✅ Completed  

---

# 🔍 Summary

Crack the Hash focuses on identifying common hash types and recovering the original plaintext values using tools such as Hash Identifier and Hashcat. The room reinforces hash recognition, selecting the correct cracking mode, and using wordlists efficiently.

---

# 🛠️ Tools Used

- Hash Identifier
- Hashcat
- RockYou Wordlist
- CrackStation

---

# 📡 Hash Identification

For each challenge hash, the first step was identifying the hash type.

Example:

```bash
hash-identifier <hash>
```

The identified algorithms included:

- MD5
- SHA1
- SHA256
- bcrypt
- HMAC-SHA1
- SHA512Crypt
- NTLM

---

# 🔓 Cracking Hashes

## MD5

Hash:

```text
48bb6e862e54f2a795ffc4e541caed4d
```

```bash
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt
```

Recovered password:

```text
easy
```

<img width="723" height="1055" alt="Screenshot 2026-06-03 093000" src="https://github.com/user-attachments/assets/20da337c-7a2b-484c-92a3-e22c4d22950d" />
<img width="1213" height="1022" alt="Screenshot 2026-06-03 093040" src="https://github.com/user-attachments/assets/0e43f313-3872-48e3-a35d-83ea2fda7f2c" />


---

## SHA1

Hash:

```text
CBFDAC6008F9CAB4083784CBD1874F76618D2A97
```

```bash
hashcat -m 100 hash.txt /usr/share/wordlists/rockyou.txt
```

Recovered password:

```text
password123
```

<img width="710" height="819" alt="Screenshot 2026-06-03 093104" src="https://github.com/user-attachments/assets/73b814c5-5676-46f8-8a1c-b95c39cec389" />
<img width="1214" height="1016" alt="Screenshot 2026-06-03 093234" src="https://github.com/user-attachments/assets/b3631f2e-5556-40e2-b797-fc8433a544ab" />


---

## SHA256

Hash:

```text
1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032
```

```bash
hashcat -m 1400 hash.txt /usr/share/wordlists/rockyou.txt
```

Recovered password:

```text
letmein
```

<img width="755" height="603" alt="Screenshot 2026-06-03 093300" src="https://github.com/user-attachments/assets/ab385886-a355-47a0-9030-77c6eafe7932" />
<img width="1210" height="980" alt="Screenshot 2026-06-03 094358" src="https://github.com/user-attachments/assets/9058ed7f-cbc3-4e11-8423-8e28e7da988b" />


---

## bcrypt

Hash type identified as:

```text
bcrypt
```

Because bcrypt is salted, a reduced custom wordlist was created before running Hashcat.

```bash
awk 'length == 4' rockyou.txt > filtered
```

```bash
hashcat -m 3200 hash.txt filtered
```

Recovered password:

```text
bleh
```

<img width="663" height="393" alt="Screenshot 2026-06-03 094535" src="https://github.com/user-attachments/assets/bcf3f7ce-2303-47ee-8e2e-935f6ae85435" />
<img width="615" height="111" alt="Screenshot 2026-06-03 094602" src="https://github.com/user-attachments/assets/41e4dfd0-12d0-42e2-914b-025134eb4fdb" />
<img width="1293" height="1091" alt="Screenshot 2026-06-03 095247" src="https://github.com/user-attachments/assets/c90d040a-8f5a-4397-ae71-b0728d3f6967" />


---

## MD4

Hash:

```text
279412f945939ba78ce0758d3fd83daa
```

RockYou did not contain the password.

Hashcat was exhausted:

```bash
hashcat -m 900 hash.txt rockyou.txt
```

The hash was then searched using CrackStation.

Recovered password:

```text
Eternity22
```

<img width="593" height="877" alt="Screenshot 2026-06-03 095336" src="https://github.com/user-attachments/assets/9838fbe9-e3df-44c6-a70e-4b1c06f2347a" />
<img width="1076" height="814" alt="Screenshot 2026-06-03 095455" src="https://github.com/user-attachments/assets/8b01b439-49d9-4ba6-9d5b-15dd5a4e22a4" />
<img width="2559" height="1277" alt="Screenshot 2026-06-03 095533" src="https://github.com/user-attachments/assets/10208055-8120-4618-a84a-9f057385f0c5" />


---

## SHA256

Hash:

```text
f09edcb1cfecf6dfb23dc3505a882655ff77375ed8aa2d1c13f640fcc2d0c85
```

```bash
hashcat -m 1400 hash.txt rockyou.txt
```

Recovered password:

```text
paule
```

<img width="622" height="459" alt="Screenshot 2026-06-03 095702" src="https://github.com/user-attachments/assets/1b0c6ed3-6c97-4939-8dc1-a362b382d131" />
<img width="1059" height="824" alt="Screenshot 2026-06-03 095822" src="https://github.com/user-attachments/assets/639c409e-f6fe-4b81-8b10-9ec3b5e8f237" />


---

## NTLM

Hash:

```text
1DFECA0C002AE40B8619ECF94819CC1B
```

```bash
hashcat -m 1000 hash.txt rockyou.txt
```

Recovered password:

```text
n63umy8lkf4i
```

<img width="625" height="870" alt="Screenshot 2026-06-03 095850" src="https://github.com/user-attachments/assets/dece277d-a8fb-4c71-a7a0-3104c979ec06" />
<img width="1071" height="835" alt="Screenshot 2026-06-03 100223" src="https://github.com/user-attachments/assets/e1c1406e-1c05-4270-bc42-a9c901e2242c" />


---

## SHA512Crypt

Hash type:

```text
SHA512Crypt
```

A filtered wordlist was generated to speed up the attack.

```bash
awk 'length == 6' rockyou.txt > filtered
```

```bash
hashcat -m 1800 hash.txt filtered
```

Recovered password:

```text
waka99
```

<img width="846" height="65" alt="Screenshot 2026-06-03 100455" src="https://github.com/user-attachments/assets/2eda2fdb-b6e5-4a81-8662-b689cf9d7c39" />
<img width="1236" height="1130" alt="Screenshot 2026-06-03 101208" src="https://github.com/user-attachments/assets/3156e88f-3a33-41af-a96f-cd33d706b0ee" />


---

## HMAC-SHA1

Hash:

```text
e5d8870e5bdd26602cab8dbe07a942c8669e56d6
```

Using the provided salt:

```text
tryhackme
```

```bash
hashcat -m 160 hash:salt rockyou.txt
```

Recovered password:

```text
481616481616
```
<img width="739" height="806" alt="Screenshot 2026-06-03 101425" src="https://github.com/user-attachments/assets/b431b6c6-d79f-42c4-a0e6-ac456fa80e5e" />
<img width="1097" height="813" alt="Screenshot 2026-06-03 101444" src="https://github.com/user-attachments/assets/b84c4bbd-e5b9-4932-8991-37e0d00b6961" />


---
<img width="1639" height="881" alt="Screenshot 2026-06-03 101501" src="https://github.com/user-attachments/assets/9468b635-653c-44af-a3f3-bed2abdee2ca" />

# 📚 Lessons Learned

- Learned to identify common hash algorithms quickly.
- Practiced selecting correct Hashcat modes.
- Understood the difference between salted and unsalted hashes.
- Learned why bcrypt and SHA512Crypt require more time to crack.
- Used custom wordlists to optimize cracking performance.
- Used CrackStation when local wordlists failed.
- Reinforced password-cracking workflow used in real-world assessments.

---
