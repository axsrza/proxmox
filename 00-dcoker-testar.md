🎞️ metube

```bash
sudo mkdir -p /srv/metube/downloads
```

```bash
sudo nano /srv/metube/docker-compose.yml
```

```bash
services:
  metube:
    image: ghcr.io/alexta69/metube
    container_name: metube
    restart: unless-stopped
    ports:
      - "8081:8081"
    volumes:
      - ./downloads:/downloads
```

```bash
cd /srv/metube
sudo docker-compose up -d
```

## 📺 Navidrome

```bash
sudo mkdir -p /srv/navidrome/data
```

```bash
sudo nano /srv/navidrome/docker-compose.yml
```

```bash
services:
  navidrome:
    image: deluan/navidrome:latest
    container_name: navidrome
    user: "${UID:-1000}:${GID:-1000}"
    ports:
      - "4533:4533"
    volumes:
      - /srv/navidrome/data:/data
      - /srv/metube/downloads:/music
    environment:
      ND_SCANINTERVAL: 1m
      ND_LOGLEVEL: info
    restart: unless-stopped
```

```bash
cd /srv/navidrome
sudo docker-compose up -d
```

## 🎞️ yt-dlp-web-ui

```bash
cd /srv/metube
sudo docker-compose up -d
```

```bash
sudo nano /srv/yt-dlp/docker-compose.yml
```

```yaml
services:
  yt-dlp:
    container_name: yt-dlp
    image: ghcr.io/marcopiovanello/yt-dlp-web-ui:latest
    ports:
      - "3033:3033"
    volumes:
      - ./config:/app/config
      - ./downloads:/downloads
    restart: unless-stopped
```

```bash
sudo nano /srv/yt-dlp/config/config.yml
```

```yaml
port: 3033
downloadPath: /downloads
require_auth: false
queue_size: 4
downloaderPath: /usr/local/bin/yt-dlp
enable_file_logging: false
log_path: /logs/yt-dlp-webui.log
local_database_path: /app/local.db
```

```bash
cd /srv/yt-dlp
sudo docker-compose up -d
```

---

## 📺 Jellyfin

```bash
sudo mkdir -p /srv/jellyfin/config
sudo mkdir -p /srv/jellyfin/media
```

```bash
sudo nano /srv/jellyfin/docker-compose.yml
```

```yaml
services:
  jellyfin:
    container_name: jellyfin
    image: jellyfin/jellyfin:latest
    ports:
      - "8096:8096"
    volumes:
      - ./config:/config
      - ../yt-dlp/downloads:/media:ro
      - ../metube/downloads:/media/metube:ro
    restart: unless-stopped
```

```bash
cd /srv/jellyfin
sudo docker-compose up -d
```

---

## 🧭 Heimdall Dashboard

```bash
sudo mkdir -p /srv/heimdall/config
```

```bash
sudo nano /srv/heimdall/docker-compose.yml
```

```yaml
services:
  heimdall:
    container_name: heimdall
    image: lscr.io/linuxserver/heimdall:latest
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Sao_Paulo
    volumes:
      - ./config:/config
    ports:
      - "81:80"
      - "444:443"
```

```bash
cd /srv/heimdall
sudo docker-compose up -d
```
