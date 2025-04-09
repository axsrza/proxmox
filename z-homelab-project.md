# 📘 Homelab — Documentação Completa

## 📁 Índice

1. [🔧 Preparação do Sistema](#1-preparação-do-sistema)  
2. [📂 Estrutura de Diretórios](#2-criar-os-diretórios-para-o-pi-hole-e-unbound)  
3. [📋 Arquivo docker-compose.yml (Pi-hole + Unbound)](#3-criar-o-arquivo-docker-composeyml)  
4. [🚀 Subir os Containers](#4-subir-os-containers)  
5. [🔐 Configurar a Senha do Pi-hole](#5-configurar-a-senha-do-pi-hole)  
6. [🧪 Testes de DNS e Containers](#6-testes)  
7. [🌐 Blog Estático com Nginx](#-estrutura-do-diretório)  
8. [☁️ Cloudflare Tunnel](#-instalar-cloudflared)  

---

## 1. Preparação do Sistema

```bash
ip -c a
sudo passwd root
su
```

Timezone e remoção de pacotes antigos:

```bash
timedatectl set-timezone America/Sao_Paulo
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
sudo apt-get update
sudo apt-get install -y ca-certificates curl
```

Instalar Docker:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian   $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
```

Instalar Docker Compose:

```bash
curl -SL https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker compose version
```

---

## 2. Criar os Diretórios para o Pi-hole e Unbound

```bash
mkdir -p ~homelab/pihole-unbound
mkdir -p ~homelab/pihole-unbound-data/pihole/etc-pihole
mkdir -p ~homelab/pihole-unbound-data/pihole/etc-dnsmasq.d
mkdir -p ~homelab/pihole-unbound-data/unbound/etc-unbound
cd ~homelab/pihole-unbound
```

---

## 3. Criar o arquivo `docker-compose.yml`

```bash
nano docker-compose.yml
```

Conteúdo:

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
    volumes:
      - /home/homelab/pihole-unbound-data/unbound/etc-unbound:/etc/unbound

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
      - /home/homelab/pihole-unbound-data/pihole/etc-pihole:/etc/pihole
      - /home/homelab/pihole-unbound-data/pihole/etc-dnsmasq.d:/etc/dnsmasq.d
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

---

## 4. Subir os Containers

```bash
docker compose up -d
```

---

## 5. Configurar a Senha do Pi-hole

```bash
docker exec -it pihole pihole setpassword
```

---

## 6. Testes

```bash
docker ps -a
docker images
docker network ls
docker volume ls
docker compose ls
docker network inspect pihole-unbound_pihole_net
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' pihole
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' unbound
dig @10.2.0.3 google.com
dig @10.2.0.2 google.com
dig +dnssec +multi dnssec-failed.org @10.2.0.2
dig +trace google.com
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

---

## 🌐 Blog Estático com Nginx

### Estrutura do diretório

```bash
mkdir -p ~homelab/blog/html
```

### Criar `index.html`

```bash
nano ~homelab/blog/html/index.html
```

Conteúdo:

```html
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

### Criar `docker-compose.yml`

```bash
nano ~homelab/blog/docker-compose.yml
```

Conteúdo:

```yaml
version: '3'

services:
  blog:
    image: nginx:alpine
    ports:
      - "8888:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
    restart: unless-stopped
```

### Subir o container

```bash
cd ~homelab/blog
chmod +x /usr/local/bin/docker-compose
docker-compose up -d
```

---

## ☁️ Instalar `cloudflared`

```bash
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
rm cloudflared-linux-amd64.deb

apt update
apt install cloudflared
```

### Autenticar com Cloudflare

```bash
cloudflared tunnel login
```

### Criar o túnel

```bash
cloudflared tunnel delete blog
cloudflared tunnel create blog
```

### Arquivo de configuração

```bash
nano /root/.cloudflared/config.yml
```

Exemplo:

```yaml
tunnel: blog
credentials-file: /root/.cloudflared/INSERIR_ID_DO_TUNNEL.json

ingress:
  - hostname: azzor1337x.shop
    service: http://127.0.0.1:8888
  - service: http_status:404
```

### Rodar e instalar como serviço

```bash
cloudflared tunnel run blog
cloudflared service install
```

