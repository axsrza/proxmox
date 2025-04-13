
## Estrutura de pastas sugerida

```bash
mkdir -p ~/homelab/media/music
mkdir -p ~/homelab/media/video
```
## yt-dlp-web-ui

```bash
sudo mkdir -p ~/homelab/yt-dlp
sudo nano ~/homelab/yt-dlp/docker-compose.yml
```

```bash
services:
  yt-dlp:
    container_name: yt-dlp
    image: ghcr.io/marcopiovanello/yt-dlp-web-ui:latest
    ports:
      - "3033:3033"
    volumes:
      - ./config:/app/config
      - ~/homelab/media:/downloads
    restart: unless-stopped
```

```bash
sudo nano ~/homelab/yt-dlp/config/config.yml
```

```bash
port: 3033
downloadPath: /downloads  # Corrigido aqui
require_auth: false
queue_size: 4
downloaderPath: /usr/local/bin/yt-dlp
enable_file_logging: false
log_path: /logs/yt-dlp-webui.log
local_database_path: /app/local.db
```

```bash
cd ~/homelab/yt-dlp
sudo docker-compose up -d
```

## Jellyfin

```bash
sudo mkdir -p ~/homelab/jellyfin
sudo nano ~/homelab/jellyfin/docker-compose.yml
```

```bash
services:
  jellyfin:
    container_name: jellyfin
    image: jellyfin/jellyfin:latest
    ports:
      - "8096:8096"
    volumes:
      - ./config:/config
      - ~/homelab/media/music:/media/music:ro
      - ~/homelab/media/video:/media/video:ro
    restart: unless-stopped
```

```bash
cd ~/homelab/jellyfin
sudo docker-compose up -d
```

## Heimdall Dashboard

```bash
sudo mkdir -p ~/homelab/heimdall
sudo nano ~/homelab/heimdall/docker-compose.yml
```

```bash
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
cd ~/homelab/heimdall
sudo docker-compose up -d
```
