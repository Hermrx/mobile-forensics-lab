# Mobile Forensics Lab – Android Device Analysis

![Autopsy](https://img.shields.io/badge/Autopsy-4.22.1-557C94?style=for-the-badge&logo=autopsy&logoColor=white)
![Android](https://img.shields.io/badge/Android-Forensics-3DDC84?style=for-the-badge&logo=android&logoColor=white)
![ALEAPP](https://img.shields.io/badge/ALEAPP-3.1.6-orange?style=for-the-badge&logoColor=white)
![Windows](https://img.shields.io/badge/Windows_11-Host-0078D4?style=for-the-badge&logo=windows&logoColor=white)

> Full mobile forensic investigation of a confiscated Android device (data.dd image), recovering messages, browser history, call logs, emails, installed apps, and SIM data — completed as part of the MCIS Master's in Cybersecurity at CEUPE European Business School.

---

## 📋 Overview

This lab demonstrates a complete mobile forensic analysis workflow against a raw Android disk image using Autopsy and ALEAPP. The investigation reconstructs a criminal plot involving stolen government password hashes, a GPU farm for cracking, and a coordinated network of accomplices.

**Lab Environment:**
| Tool | Version | Role |
|---|---|---|
| Autopsy | 4.22.1 | Primary forensic platform |
| ALEAPP | 3.1.6 | Android artifact parser & report generator |
| Windows 11 | Host OS | Analysis workstation |

**Device Under Investigation:**
| Attribute | Value |
|---|---|
| Model | LG Nexus 4 |
| OS | Android 9 (Pie) |
| Phone Number | +15555215554 |
| Carrier | T-Mobile |
| SIM ICCID | 89014103211118510720 |
| Gmail Account | ceupe.forensics@gmail.com |

---

## 🛠️ Tools Used

| Tool | Purpose |
|------|---------|
| Autopsy 4.22.1 | Disk image ingestion, file carving, artifact extraction |
| ALEAPP 3.1.6 | Android-specific log parsing (SMS, calls, accounts, apps) |
| Android Analyzer (Autopsy module) | Automated Android artifact detection |
| SQLite Browser (via Autopsy) | Manual DB inspection (browser2.db, contacts2.db, mailstore) |

---

## 📁 Investigation Structure

### Question 1 — What was Mr. X plotting?

- Imported `data.dd` into Autopsy as a disk image source
- Ran Android Analyzer module to extract artifacts
- Generated ALEAPP report to parse SMS messages in chronological order
- **Finding:** Mr. X obtained a **shadow file from a three-letter U.S. government agency** (NSA/CIA/FBI/DEA/DOD) and was attempting to crack the password hashes
- Browser history confirmed he searched extensively for password cracking tools and techniques

---

### Question 2 — Who did he ask for help first?

- Reviewed ALEAPP SMS report sorted chronologically
- **Finding:** First contact was **Matt Murdock** (650-555-1111) on May 8, 2019 at 11:10 PM
- Murdock warned Mr. X he was "playing with fire" and could not help directly, but referred him to Danny Rand (650-555-2222)

---

### Question 3 — Who tried to help? For how much?

- Traced SMS thread between Mr. X and Danny Rand (650-555-2222)
- **Finding:** Danny Rand agreed to rent his GPU farm for password cracking at **$10,000 per week**
- Payment was made via Bitcoin to wallet `1MCwBbhNGp5hRm5rC1Aims2YFRe2SXPYKt`

---

### Question 4 & 5 — Who provided the file link? What was the link?

- Identified SMS thread with **Jessica Jones** (650-555-4444), contacted May 9, 2019 at 1:32 AM
- Jones uploaded the shadow file to a remote server and shared the link with Mr. X
- **Shadow file URL:** `https://cyberhades.ams3.digitaloceanspaces.com/shadow`
- Mr. X forwarded the link to Danny Rand for feasibility evaluation

---

### Browser History & Cookies

- Browser database located at `/data/com.android.browser/databases/browser2.db`
- History exported as `history.csv` — key entries include:
  - Direct access to the shadow file URL
  - Google searches for "how to crack a password"
  - Visits to `guru99.com` and `infosecinstitute.com` (password cracking guides)
- Cookies extracted from `/data/com.android.browser/app_webview` — **301 entries** exported as `cookies.csv`

---

### Contact List

| Name | Phone Number | Role in Plot |
|---|---|---|
| Matt Murdock | 650-555-1111 | Initial contact — referred Mr. X to Danny Rand |
| Danny Rand | 650-555-2222 | GPU farm owner — rented cracking infrastructure |
| Jessica Jones | 650-555-4444 | Provided the shadow file link |
| Frank Castle | 650-555-3333 | Linked via calls and Jessica Jones messages |

---

### Call Log

- Located in `contacts2.db` at `/data/com.android.providers.contacts/databases/`
- Three relevant calls recovered:
  - **555-123-4444** — Unknown contact, May 8 at 7:06 PM, ~2 minutes (possible source of shadow file)
  - **Frank Castle** (650-555-3333) — Two very short calls on May 8 at 6:16 PM and 6:17 PM, geolocated to **California**

---

### Email Evidence

- Mail database at `/data/com.android.gm/databases/mailstore.ceupe.forensics@gmail.com.db`
- Two emails recovered:
  - Email to `tuxotron@gmail.com` on May 6, 2019 — no subject, contained an image attachment
  - **Critical email from `hoamm9+glo8prxac4uo1u@guerrillamail.com`** on May 9, 2019 at 5:04 PM — subject: **"GPU farm creds"** — contained login credentials for `http://ultrarender.com/login`

---

### OS Version

- `build.prop` file was **deleted** from the device — confirmed via ALEAPP script run log
- Version inferred from: SMS message containing `"Yumm! Pie à la Android mode!"` — interpreted as a reference to **Android 9 (Pie)**
- Device model confirmed via Google welcome email in SQLite journal: **LG Nexus 4**

---

### WiFi Hotspot Data

- SSID: `AndroidAP`
- Passphrase: `f7e1f191896f`
- Source: ALEAPP hotspot report

---

### Device Usage Timeline

- Device was **inactive since 2016**
- Reactivated on **May 8, 2019** specifically for the events described
- All criminal activity occurred within a **36-hour window** (May 8–9, 2019)

---

## 🔑 Key Findings Summary

| Item | Value |
|---|---|
| Device | LG Nexus 4 — Android 9 |
| Phone Number | +15555215554 (T-Mobile) |
| Gmail | ceupe.forensics@gmail.com |
| Target | Shadow file from a U.S. three-letter agency |
| Shadow file URL | https://cyberhades.ams3.digitaloceanspaces.com/shadow |
| GPU farm URL | http://ultrarender.com/login |
| GPU farm credentials | rent20190099 / ren7GPU!)@(#* |
| Payment method | Bitcoin — 1MCwBbhNGp5hRm5rC1Aims2YFRe2SXPYKt |
| Weekly cost | $10,000 |

---

## 🔎 Recommended Follow-up Investigation

- Trace the unknown call to **555-123-4444** — potential origin of the shadow file
- Seize and analyze devices belonging to **Frank Castle** and **Jessica Jones**
- Investigate **Danny Rand's GPU farm** (`ultrarender.com`) to determine if cracking was completed

---

## ⚠️ Disclaimer

All analysis was performed on a forensic disk image in a controlled academic environment as part of the MCIS – Master's in Cybersecurity program at CEUPE European Business School. No real-world systems or individuals were targeted.

---

## 👤 Author

**Herminio José Aquino Ramos**  
MCIS – Master's in Cybersecurity | CEUPE European Business School  
ISO 27001 & ISO 22301 Internal Auditor | TÜV Nord Certified  
[LinkedIn](https://www.linkedin.com/in/herminio-aquino-4113ba2b0)
