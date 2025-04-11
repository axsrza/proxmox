# Homelab Setup - Debian 12 Minimal

Este documento descreve todo o processo de configuração de um servidor homelab com Debian 12 minimal. O foco está na configuração de rede, instalação de Docker, e o deploy de serviços como Portainer, Nginx, Cloudflared, Home Assistant, Unbound e Pi-hole.

---

## Índice

1. [Configuração Inicial e Rede](#1-configura%C3%A7%C3%A3o-inicial-e-rede)
2. [Instalação do Docker e Docker Compose](#2-instala%C3%A7%C3%A3o-do-docker-e-docker-compose)
3. [Portainer](#3-portainer)
4. [Servidor Web com Nginx (Blog)](#4-servidor-web-com-nginx-blog)
5. [Cloudflared Tunnel](#5-cloudflared-tunnel)
6. [Home Assistant](#6-home-assistant)
7. [Unbound](#7-unbound)
8. [Pi-hole](#8-pi-hole)
9. [Pi-hole como Servidor DHCP](#9-pi-hole-como-servidor-dhcp)

---

## 1. Configuração Inicial e Rede

> Referência: https://wiki.debian.org/SystemdNetworkd

```bash
ip -c a
```

```bash
sudo passwd root
su
```

```bash
sudo timedatectl set-timezone America/Sao_Paulo
```

```bash
sudo rm /etc/udev/rules.d/10-network.rules
sudo nano /etc/udev/rules.d/10-network.rules
```

```bash
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:e0:4c:68:00:94", NAME="eth0"
```

```bash
sudo rm /etc/systemd/network/10-wired.network
sudo nano /etc/systemd/network/10-wired.network
```

```bash
[Match]
Name=eth0

[Network]
Address=192.168.1.99/24
Gateway=192.168.1.1
DNS=192.168.1.1
#DNS=127.0.0.1
```

```bash
sudo rm /etc/resolv.conf
sudo nano /etc/resolv.conf
```

```bash
nameserver 192.168.1.1
#nameserver 127.0.0.1
```

```bash
sudo rm /etc/network/interfaces
```

```bash
sudo systemctl enable --now systemd-networkd
sudo systemctl restart systemd-networkd
sudo systemctl disable --now networking
sudo reboot
```

---

## 2. Instalação do Docker e Docker Compose

> Referência: https://docs.docker.com/engine/install/debian/

```bash
sudo apt install iptables btop -y
```

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian   $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
sudo curl -SL https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -sf /usr/local/bin/docker-compose /usr/bin/docker-compose
sudo docker compose version
```

---

## 3. Portainer

> Referência: https://docs.portainer.io/start/install-ce/server/docker/linux

```bash
sudo mkdir -p ~homelab/portainer
```

```bash
sudo nano ~homelab/portainer/docker-compose.yml
```

```bash
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data

volumes:
  portainer_data:
```

```bash
cd ~homelab/portainer
sudo docker-compose up -d
```

---

## 4. Servidor Web com Nginx (Blog)

> Referência: https://hub.docker.com/_/nginx

```bash
sudo mkdir -p ~homelab/blog/html
```

```bash
sudo nano ~homelab/blog/html/index.html
```

```bash
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>azzor1337x</title>
</head>
<body>
  <h1>azzor1337x</h1>
  <p>Hello World!</p>
</body>
</html>
```

```bash
sudo nano ~homelab/blog/docker-compose.yml
```

```bash
services:
  blog:
    image: nginx:alpine
    ports:
      - "8888:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
    restart: unless-stopped
```

```bash
cd ~homelab/blog
sudo docker-compose up -d
```

```bash
ip -c a
hostname -I
docker ps -a
docker images
docker network ls
docker volume ls
docker compose ls
docker network inspect portainer_default
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' portainer
docker network inspect blog_default
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' blog-blog-1
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

---

## 5. Cloudflared Tunnel

> Referência: https://developers.cloudflare.com/cloudflared/

```bash
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
rm cloudflared-linux-amd64.deb
apt update
apt install cloudflared
cloudflared tunnel login
```

```bash
cloudflared tunnel delete blog
cloudflared tunnel create blog  # ID será gerado aqui
```

```bash
nano /root/.cloudflared/config.yml
```

```bash
tunnel: blog
credentials-file: /root/.cloudflared/INSERIR_ID_DO_TUNNEL.json # Inserir o ID gerado aqui

ingress:
  - hostname: azzor1337x.shop
    service: http://127.0.0.1:8888
  - service: http_status:404
```

```bash
cloudflared tunnel run blog
```

```bash
cloudflared service install
```

---

## 6. Home Assistant

> Referência: https://www.home-assistant.io/installation/linux

```bash
sudo mkdir -p ~homelab/homeassistant
sudo nano ~homelab/homeassistant/docker-compose.yml
```

```bash
services:
  homeassistant:
    container_name: homeassistant
    image: ghcr.io/home-assistant/home-assistant:stable
    volumes:
      - /home/homelab/homeassistant/config:/config
      - /etc/localtime:/etc/localtime:ro
      - /run/dbus:/run/dbus:ro
    restart: unless-stopped
    privileged: true
    network_mode: host
    environment:
      - TZ=America/Sao_Paulo
```

```bash
cd ~homelab/homeassistant
sudo docker-compose up -d
```

---

## 7. Unbound

## Instalar unbound

```bash
sudo apt install unbound
```

## Colar no Terminal

```bash
sudo wget https://www.internic.net/domain/named.root -qO- | sudo tee /var/lib/unbound/root.hints
```

## Config unbound

```bash
sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf
```

## Config unbound

```bash
# [conteúdo igual ao anterior]
```

## Teste unbound

```bash
sudo service unbound restart
dig pi-hole.net @127.0.0.1 -p 5335
```

---

## 8. Pi-hole

> Referência: https://github.com/pi-hole/docker-pi-hole/

```bash
sudo mkdir -p ~/homelab/pihole/
```

```bash
sudo nano ~/homelab/pihole/docker-compose.yml
```

```bash
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    network_mode: host
    environment:
      TZ: 'America/Sao_Paulo'
      WEBPASSWORD: ''
    volumes:
      - './etc-pihole:/etc/pihole'
      - './etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
```

```bash
cd ~/homelab/pihole
sudo docker-compose up -d
```

```bash
docker exec -it pihole pihole setpassword
```

---

## 9. Pi-hole como Servidor DHCP

1. Acesse o painel web do Pi-hole: `http://192.168.1.99/admin`
2. Vá em **Settings > DHCP**
3. Marque a opção **Enable DHCP server**
4. Configure os campos:
   - **Range**: 192.168.1.100 to 192.168.1.200
   - **Router (gateway)**: 192.168.1.1
   - **DNS server**: 192.168.1.99
5. Clique em **Save** para ativar

> Obs.: Desative o servidor DHCP do seu roteador antes de ativar o do Pi-hole.

---
