---

## 📺 metube

```bash
mkdir -p /home/homelab/music
```

```bash
mkdir -p /opt/metube
nano /opt/metube/docker-compose.yml
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
      - /home/homelab/music:/downloads
```

```bash
cd /opt/metube
docker-compose up -d
```

---
