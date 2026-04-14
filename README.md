# Mobile Forensics Lab — Android Device Analysis

## Overview
Digital forensic investigation of an Android mobile device conducted in a controlled 
lab environment as part of the CEUPE European Business School Cybersecurity 
Master's program. The analysis aimed to reconstruct a timeline of criminal activity 
involving password cracking of government agency credentials.

## Case Summary
A suspect device (LG Nexus 4, Android 9) was analyzed to determine the activities 
of a subject identified as "Mr. X." The device had been inactive since 2016 and was 
reactivated on May 8, 2019 to coordinate a series of criminal communications.

## Tools Used
| Tool | Purpose |
|------|---------|
| Autopsy 4.22.1 | Primary forensic analysis platform |
| ALEAPP | Android logs, events and protobuf parser |
| SQLite Browser | Database file examination |

## Evidence Recovered
- SMS message history (114 messages) revealing criminal coordination
- Browser history including shadow file download URL
- Contact list with 4 suspects and their roles
- Call logs with timestamps and geolocation data
- Gmail account (ceupe.forensics@gmail.com)
- Email containing GPU farm credentials
- Device model identified via SQLite journal (LG Nexus 4)
- SIM card data — ICCID: 89014103211118510720 / Carrier: T-Mobile
- WiFi hotspot credentials (AndroidAP)
- Recovered images from carved files

## Key Findings
| Finding | Detail |
|---------|--------|
| Device | LG Nexus 4 |
| OS | Android 9 (Pie) |
| Phone number | +15555215554 |
| Active account | ceupe.forensics@gmail.com |
| Criminal activity | Attempted cracking of government agency shadow file |
| GPU farm cost | $10,000/week via Bitcoin wallet |
| Shadow file URL | https://cyberhades.ams3.digitaloceanspaces.com/shadow |

## Suspects Identified
| Name | Phone | Role |
|------|-------|------|
| Matt Murdock | 650-555-1111 | Initial contact / referral |
| Danny Rand | 650-555-2222 | GPU farm provider |
| Jessica Jones | 650-555-4444 | Shadow file supplier |
| Frank Castle | 650-555-3333 | Unknown — 2 calls recorded |

## Methodology
1. Device image import (data.dd) into Autopsy
2. Android Analyzer module execution
3. ALEAPP report generation
4. SMS, browser history, contacts and call log analysis
5. SQLite database examination
6. Keyword search and carved file recovery
7. Timeline reconstruction

## Full Report
See attached PDF for complete analysis with screenshots and evidence documentation.

## Author
Herminio José Aquino Ramos
Cybersecurity Graduate | ISO 27001 & ISO 22301 Internal Auditor (TÜV Nord)
