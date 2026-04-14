# Docker Security Lab

## Overview
Containerization security exercises conducted in Kali Linux as part of the 
CEUPE European Business School Cybersecurity Master's program. 
Two practical implementations demonstrating Docker security best practices.

## Exercise 1 — ASCII Art Converter
A Docker container that converts JPEG/PNG images to ASCII art using jp2a.

### Tools & Technologies
- Docker
- Debian bookworm-slim base image
- jp2a (image to ASCII converter)
- Persistent volumes

### Security Practices Applied
- Non-privileged user (appuser) inside container
- Minimal base image to reduce attack surface
- Read-only input mount (:ro)
- No unnecessary dependencies installed
- Temporary files cleaned after build

---

## Exercise 2 — WordPress + MySQL Architecture
A full WordPress deployment with persistent data using both Dockerfile 
and Docker Compose approaches.

### Tools & Technologies
- Docker / Docker Compose
- WordPress (php8.2-apache)
- MySQL 8.0 / MariaDB
- Apache2
- Persistent volumes

### Security Practices Applied
- Service separation (WordPress and DB in separate containers)
- Internal Docker network — MySQL not exposed externally
- Official images only
- Data persistence via volumes (survives container restarts)
- Minimal installation with --no-install-recommends
- restart: always for automatic recovery

## Full Reports
See attached PDFs for complete documentation with screenshots.

## Author
Herminio José Aquino Ramos
Cybersecurity Graduate | ISO 27001 & ISO 22301 Internal Auditor (TÜV Nord)
## Full Report
See [Fundamentos_de_Seguridad_Cloud_e_infraestructuras_industriales_HerminioAquino.pdf](Fundamentos_de_Seguridad_Cloud_e_infraestructuras_industriales_HerminioAquino.pdf) 
for complete documentation with screenshots.
