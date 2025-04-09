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
        ipv4_address: 10.2.0.2 # IP fixo dentro da rede interna do Docker para garantir comunicação confiável com o Pi-hole

  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    hostname: pihole
    domainname: lan
    restart: unless-stopped
    environment:
      TZ: "America/Sao_Paulo"
      WEBPASSWORD: "" # Senha em branco — será definida depois via comando docker exec
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
# Definir a senha de acesso do painel do Pi-hole:
docker exec -it pihole pihole setpassword
```

```bash
# Acesse o Pi-hole no navegador:
# http://IP_DO_HOMELAB/admin
# Use a senha definida no comando acima
```

```bash
# Após testar o funcionamento do Pi-hole como DHCP, desative o DHCP no seu modem/roteador.
```
