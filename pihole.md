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
    ports:
      - "5353:53/tcp"
      - "5353:53/udp"
      - "6767:67/udp" # Apenas necessário se estiver usando o Pi-hole como servidor DHCP
      - "8080:80/tcp"
    environment:
      TZ: 'America/Sao_Paulo'
      # WEBPASSWORD: 'suasenha'
    volumes:
      - '/root/pihole/etc-pihole:/etc/pihole'
      - '/root/pihole/etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      - NET_ADMIN # Required if you are using Pi-hole as your DHCP server, else not needed
    restart: unless-stopped

```

## Alterar a senha

```bash
docker exec -it pihole sudo pihole -a -p

```
