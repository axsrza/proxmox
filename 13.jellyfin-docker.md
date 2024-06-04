## Instalação jellyfin

```bash
cd /
cd
mkdir -p /root/jellyfin
cd /root/jellyfin
nano docker-compose.yml

```

## docker-compose.yml

```bash
version: '3.7'

services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    environment:
      - PUID=0
      - PGID=0
      - TZ=Etc/UTC
    volumes:
      - ./config:/config
      - /root/hdexterno1:/media/hdexterno1
      - /root/hdexterno2:/media/hdexterno2
    ports:
      - "8096:8096"
      - "8920:8920"
    restart: unless-stopped

```

## Executar jellyfin

```bash
sudo docker compose up -d

```
