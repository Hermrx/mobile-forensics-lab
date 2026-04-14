# Docker Lab – Containerized Applications & Security

![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Kali Linux](https://img.shields.io/badge/Kali_Linux-557C94?style=for-the-badge&logo=kali-linux&logoColor=white)
![WordPress](https://img.shields.io/badge/WordPress-21759B?style=for-the-badge&logo=wordpress&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)

> Docker lab covering containerized image processing (ASCII art converter) and a full WordPress + MySQL deployment with persistent volumes — implemented with both Dockerfile and Docker Compose — completed as part of the MCIS Master's in Cybersecurity at CEUPE European Business School.

---

## 📋 Overview

This lab demonstrates practical Docker skills: building custom images, enforcing security best practices inside containers, managing persistent volumes, and orchestrating multi-service architectures with Docker Compose.

**Lab Environment:**
| Machine | Role |
|---|---|
| Kali Linux 2025.4 (VirtualBox VM) | Docker host |
| Docker Engine (via `docker.io`) | Container runtime |
| Docker Compose Plugin v2.32.4-3 | Multi-container orchestration |

---

## 🛠️ Tools & Images Used

| Tool / Image | Purpose |
|------|---------|
| `debian:bookworm-slim` | Lightweight base image for both exercises |
| `jp2a` | JPEG/PNG to ASCII art conversion |
| `mysql:8.0` | Official MySQL database image (Compose) |
| `wordpress:php8.2-apache` | Official WordPress image (Compose) |
| Apache2 + PHP + MariaDB | Web stack for single-container WordPress |
| Docker Volumes | Persistent storage for DB and WordPress data |

---

## 📁 Lab Structure

### Exercise A — ASCII Art Converter (jp2a)

**Objective:** Build a Docker image that converts any JPEG/PNG image into ASCII art and saves the result to a persistent volume.

---

#### A.1 — Project Setup

```bash
mkdir ~/docker-asciiart
cd ~/docker-asciiart
```

---

#### A.2 — Dockerfile

```dockerfile
FROM debian:bookworm-slim

RUN apt-get update \
  && apt-get install -y --no-install-recommends jp2a ca-certificates \
  && rm -rf /var/lib/apt/lists/*

RUN useradd -m -u 10001 appuser

WORKDIR /app
COPY --chmod=755 entrypoint.sh /app/entrypoint.sh
RUN mkdir -p /asciiart && chown -R appuser:appuser /asciiart

USER appuser
ENTRYPOINT ["/app/entrypoint.sh"]
```

**Key design decisions:**
- Slim base image to minimize attack surface and image size
- `jp2a` installed as the sole dependency — no extras
- Non-root user (`appuser`, UID 10001) to reduce privilege exposure
- `/asciiart` volume directory pre-created with correct ownership

---

#### A.3 — Entrypoint Script (`entrypoint.sh`)

```bash
#!/usr/bin/env bash
set -e

if [ $# -lt 1 ]; then
  echo "Usage: docker run -v asciiart:/asciiart -v /path:/input asciiart:1 /input/image.jpg"
  exit 1
fi

INPUT="$1"
if [ ! -f "$INPUT" ]; then
  echo "ERROR: file not found: $INPUT"
  exit 2
fi

rm -f /asciiart/*
jp2a --width=220 "$INPUT" > /asciiart/output.txt
echo "ASCII art saved to /asciiart/output.txt"
```

**Logic flow:**
- Validates that an image path was passed as an argument
- Verifies the file exists before proceeding
- Clears any previous results from the volume (clean execution guarantee)
- Generates `output.txt` at fixed 220-character width

---

#### A.4 — Build & Run

```bash
# Set execution permissions
chmod +x entrypoint.sh

# Build image
sudo docker build -t asciiart:1 .

# Create persistent volume
sudo docker volume create asciiart

# Run converter
sudo docker run --rm \
  -v asciiart:/asciiart \
  -v $HOME/Desktop:/input:ro \
  asciiart:1 "/input/image.jpg"

# Verify output
sudo docker run --rm -v asciiart:/asciiart debian:bookworm-slim cat /asciiart/output.txt
```

---

#### A.5 — Security & Best Practices Applied

| Practice | Implementation |
|---|---|
| Non-root execution | `appuser` (UID 10001) runs the container process |
| Minimal base image | `debian:bookworm-slim` — reduced attack surface |
| No unnecessary packages | `--no-install-recommends` flag on apt install |
| Cache cleanup | `rm -rf /var/lib/apt/lists/*` after installation |
| Read-only input mount | `-v /path:/input:ro` prevents accidental file modification |
| Clean volume per run | Previous results deleted before each execution |

---

### Exercise B — WordPress + MySQL (Dockerfile & Docker Compose)

**Objective:** Deploy a fully functional WordPress site backed by MySQL with data persistence — first via a single custom Dockerfile, then with Docker Compose separating services into dedicated containers.

---

#### B.1 — Single-Container Deployment (Dockerfile)

**Stack:** Apache2 + PHP + MariaDB + WordPress — all in one container

```bash
mkdir -p ~/docker-wp
cd ~/docker-wp
```

**Dockerfile highlights:**
- Base: `debian:bookworm-slim`
- Installs: Apache2, PHP, `php-mysql`, MariaDB, `curl`
- Downloads and unpacks latest WordPress into `/var/www/html/`
- Exposes port 80
- Delegates startup logic to `start.sh`

**`start.sh` startup script logic:**
1. Starts MariaDB service
2. Creates `wordpress` database and `wpuser` if they don't exist (idempotent)
3. On first run: copies WordPress files to persistent volume at `/data/wp`
4. Symlinks `/var/www/html/wordpress` → `/data/wp` for persistence
5. Starts Apache in foreground (`apachectl -D FOREGROUND`)

```bash
# Build image
sudo docker build -t wp-single:1 .

# Create volumes
sudo docker volume create wp_db_data
sudo docker volume create wp_wp_data

# Run container
sudo docker run -d \
  --name wp_single \
  -p 8080:80 \
  -v wp_db_data:/data/db \
  -v wp_wp_data:/data/wp \
  wp-single:1

# Verify
sudo docker ps
sudo docker logs --tail 50 wp_single
```

**Access:** `http://localhost:8080/wordpress`

---

#### B.2 — Multi-Container Deployment (Docker Compose)

**Stack:** Official `wordpress:php8.2-apache` + `mysql:8.0` — isolated containers with internal networking

```bash
sudo apt install -y docker-compose-plugin
```

**`docker-compose.yml`:**

```yaml
services:
  db:
    image: mysql:8.0
    container_name: wp_db
    restart: always
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppassword
      MYSQL_ROOT_PASSWORD: rootpassword
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:php8.2-apache
    container_name: wp_app
    restart: always
    depends_on:
      - db
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppassword
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wp_data:/var/www/html

volumes:
  db_data:
  wp_data:
```

```bash
# Start stack
sudo docker-compose up -d

# Verify services
sudo docker-compose ps
```

**Access:** `http://localhost:8080`

---

#### B.3 — Security & Best Practices Applied

| Practice | Single Container | Docker Compose |
|---|---|---|
| Data persistence | Volumes for DB and WordPress | Volumes for DB and WordPress |
| Service isolation | Single container | Separate containers per service |
| DB not externally exposed | DB on localhost only | MySQL on Docker internal network only |
| Official images | `debian:bookworm-slim` | `mysql:8.0`, `wordpress:php8.2-apache` |
| Automatic recovery | N/A | `restart: always` on both services |
| Reproducibility | Manual build steps | Single `docker-compose.yml` recreates full stack |
| Minimal packages | `--no-install-recommends` + apt cleanup | Handled by upstream official images |

---

## 🔑 Key Takeaways

- Running containers as non-root users significantly limits the blast radius of any exploit within the container
- Slim base images reduce both download time and attack surface — fewer packages = fewer CVEs
- Persistent volumes are essential for stateful services: without them, all WordPress content and DB data is lost on container restart
- Docker Compose separates concerns cleanly — the DB has no business being accessible from outside the Docker network
- `restart: always` is a simple but effective availability control for services that need to survive host reboots

---

## ⚠️ Disclaimer

All exercises were performed in an isolated VirtualBox environment for educational purposes only, as part of the MCIS – Master's in Cybersecurity program at CEUPE European Business School. No production systems were affected.

---

## 👤 Author

**Herminio José Aquino Ramos**  
MCIS – Master's in Cybersecurity | CEUPE European Business School  
ISO 27001 & ISO 22301 Internal Auditor | TÜV Nord Certified  
[LinkedIn](https://www.linkedin.com/in/herminio-aquino-4113ba2b0)
