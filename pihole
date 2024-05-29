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

# More info at https://github.com/pi-hole/docker-pi-hole/ and https://docs.pi-hole.net/
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    # For DHCP it is recommended to remove these ports and instead add: network_mode: "host"
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "67:67/udp" # Only required if you are using Pi-hole as your DHCP server
      - "80:80/tcp"
    environment:
      TZ: 'America/Sao_Paulo'
      # WEBPASSWORD: 'segredo0'
    # Volumes store your data between container upgrades
    volumes:
  - '/root/pihole/etc-pihole:/etc/pihole'
  - '/root/pihole/etc-dnsmasq.d:/etc/dnsmasq.d'
    #   https://github.com/pi-hole/docker-pi-hole#note-on-capabilities
    cap_add:
      - NET_ADMIN # Required if you are using Pi-hole as your DHCP server, else not needed
    restart: unless-stopped

```
