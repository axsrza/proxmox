## Intalação pihole

```bash
mkdir pihole
cd pihole
touch docker-compose.yml
nano docker-compose.yml # copy the contents from below docker-compose.yml

docker compose up -d --force-recreate

```

## docker-compose.yml

```bash
version: "3"

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    network_mode: "host"
    environment:
      TZ: 'America/Sao_Paulo'
    volumes:
      - '/root/pihole/etc-pihole:/etc/pihole'
      - '/root/pihole/etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      - NET_ADMIN
    restart: unless-stopped

```

## Alterar a senha

```bash
docker exec -it pihole sudo pihole -a -p

```
