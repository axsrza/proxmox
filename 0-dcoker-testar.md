
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

```yaml
services:
  yt-dlp:
    container_name: yt-dlp
    image: ghcr.io/marcopiovanello/yt-dlp-web-ui:latest
    ports:
      - "8080:8080"
    volumes:
      - ./config:/app/config
      - ../media:/downloads
    restart: unless-stopped
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

```yaml
services:
  jellyfin:
    container_name: jellyfin
    image: jellyfin/jellyfin:latest
    ports:
      - "8096:8096"
    volumes:
      - ./config:/config
      - ../media/music:/media/music:ro
      - ../media/video:/media/video:ro
    restart: unless-stopped
```

```bash
cd ~/homelab/jellyfin
sudo docker-compose up -d
```
