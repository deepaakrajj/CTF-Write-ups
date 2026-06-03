# OhSINT — TryHackMe

**Date:** June 2026  
**Difficulty:** Easy  
**Category:** OSINT, Social Media Investigation, Metadata Analysis  
**Status:** ✅ Completed  

---

# 🔍 Summary

OhSINT is an introductory OSINT challenge that focuses on gathering information from publicly available sources. The objective was to extract metadata from a provided image and pivot through various online platforms to uncover hidden information and answer a series of investigative questions.

---

# 🛠️ Tools Used

- ExifTool
- Google Search
- GitHub
- WordPress
- X (Twitter)
- Wigle.net

---

# 📡 Initial Analysis

## Metadata Extraction

The challenge provided an image file.

Using ExifTool:

```bash
exiftool WindowsXP_1551719014755.jpg
```

<img width="627" height="517" alt="Screenshot 2026-06-03 085617" src="https://github.com/user-attachments/assets/33b7e71b-4a7c-46c2-a564-4f98bd1fcab6" />


### Findings

```text
Copyright: OWoodflint

GPS Latitude: 54 deg 17' 41.27" N
GPS Longitude: 2 deg 15' 1.33" W
```

The username:

```text
OWoodflint
```

became the starting point for the investigation.

---

# 🔎 Username Investigation

## Google Search

Searching for:

```text
OWoodflint
```

revealed several relevant accounts and websites.

<img width="2362" height="1277" alt="Screenshot 2026-06-03 085719" src="https://github.com/user-attachments/assets/0c06c27c-2ff0-4dc2-96c7-b10e2c689c3b" />


Discovered resources:

- GitHub profile
- WordPress blog
- X account

---

## X Profile

The X profile contained useful information.

<img width="1276" height="1123" alt="Screenshot 2026-06-03 085732" src="https://github.com/user-attachments/assets/c4d16af6-55d7-484c-a314-eb2f68edf789" />


A post revealed a WiFi BSSID:

```text
B4:5D:50:AA:86:41
```

---

## GitHub Profile

The GitHub repository contained personal information.

<img width="2353" height="1315" alt="Screenshot 2026-06-03 090206" src="https://github.com/user-attachments/assets/3da66fc9-075a-4bb0-b18d-ccf80c8f0283" />


Interesting details included:

```text
Location: London
Email: OWoodflint@gmail.com
```

---

## WordPress Blog

The WordPress blog revealed another clue.

<img width="2358" height="1208" alt="Screenshot 2026-06-03 092103" src="https://github.com/user-attachments/assets/aa283495-3f3e-491c-9c64-19b5e21ead3b" />


The author mentioned:

```text
I'm in New York right now
```

This helped answer one of the room questions.

---

# 🌍 Geolocation Investigation

## Wigle Search

Using the WiFi BSSID from the X profile:

```text
B4:5D:50:AA:86:41
```

I searched Wigle.net.

<img width="1814" height="714" alt="Screenshot 2026-06-03 091555" src="https://github.com/user-attachments/assets/e1a798cd-09b1-4dc7-af00-debfdf1ca603" />


The network location was identified.

This provided the geographic information required by the challenge.

---

# 🚩 Answers Discovered

Through the investigation the following information was obtained:

### User Name

```text
OWoodflint
```

### Email Address

```text
OWoodflint@gmail.com
```

### City

```text
London
```

### Current Location

```text
New York
```

### WiFi BSSID

```text
B4:5D:50:AA:86:41
```

### Additional Clue

The blog contained:

```text
pennYDr0pper!
```

<img width="2367" height="1213" alt="Screenshot 2026-06-03 092224" src="https://github.com/user-attachments/assets/023abca5-d9c1-466e-9a4e-af1a4119c497" />


---
<img width="1545" height="810" alt="Screenshot 2026-06-03 092243" src="https://github.com/user-attachments/assets/bc52fe98-f63b-4557-b310-090dbdfed264" />

# 📚 Lessons Learned

- Practiced extracting metadata from images
- Learned how GPS coordinates can expose location information
- Used username pivoting techniques
- Investigated GitHub profiles for intelligence gathering
- Gathered information from public social media accounts
- Used Wigle to map WiFi network identifiers
- Practiced correlating information from multiple public sources
- Learned the fundamentals of Open Source Intelligence (OSINT)

---
