## Tenho um notebook antigo que vou transformar em um homelab, ele é um AMD C-60 APU with Radeon(tm) HD Graphics  CPU @ 1.0GHz, com duas memorias de 4gb e um ssd de 120gb.
Acabo de formata-lo e instalei o Debian 12 minimal, no momento da instalação selecionei apenas ssh e standard, ao final da instação, começo com os comandos abaixo:

```bash
ip -c a
```

```bash
sudo passwd root
```

```bash
su
```

```bash
timedatectl set-timezone America/Sao_Paulo
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
curl -SL https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker compose version
```

```bash
mkdir -p ~/homelab/pihole-unbound
cd ~/homelab/pihole-unbound
nano docker-compose.yml
```

```yaml
version: "3"

services:
  unbound:
    image: mvance/unbound:latest
    container_name: unbound
    restart: unless-stopped
    networks:
      pihole_net:
        ipv4_address: 10.2.0.2

  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    hostname: pihole
    domainname: lan
    restart: unless-stopped
    environment:
      TZ: "America/Sao_Paulo"
      WEBPASSWORD: ""
    volumes:
      - ./pihole/etc-pihole:/etc/pihole
      - ./pihole/etc-dnsmasq.d:/etc/dnsmasq.d
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "67:67/udp"
      - "80:80"
      - "443:443"
    networks:
      pihole_net:
        ipv4_address: 10.2.0.3
    cap_add:
      - NET_ADMIN

networks:
  pihole_net:
    driver: bridge
    ipam:
      config:
        - subnet: 10.2.0.0/29
```

```bash
docker compose up -d
```

```bash
docker exec -it pihole pihole setpassword
```

```bash
docker ps -a
docker images
docker network ls
docker network inspect pihole-unbound_pihole_net
docker volume ls
docker compose ls
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' pihole
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' unbound
dig +trace google.com
dig +dnssec +multi dnssec-failed.org @10.2.0.2
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```
