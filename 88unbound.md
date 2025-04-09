# 🏠 Homelab Setup com Pi-hole e Unbound (via Docker)

<!-- Comentário: Documento iniciado em 08/04/2025 para registrar passo a passo da montagem do homelab. Atualizado em 09/04/2025 com rede macvlan e recomendações de segurança. -->

## 📋 Índice

1. [Primeiros passos após instalação](#-primeiros-passos-após-instalação)
2. [Instalar Docker e Docker Compose](#1-instalar-docker-engine-e-docker-compose)
3. [Pi-hole via Docker + macvlan](#3-instalar-pi-hole-via-docker)
4. [Unbound via Docker](#4-instalar-unbound-via-docker)
5. [Configurar Pi-hole para usar Unbound](#5-configurar-o-pi-hole-para-usar-o-unbound)
6. [Segurança e arquitetura](#segurança-e-arquitetura)
7. [Comandos úteis](#-docker---estado-atual-do-ambiente)

---

## 🧭 Primeiros passos após instalação

Após finalizar a instalação do Debian 12 minimal, no terminal local logar com o usuário criado, verifique o IP e Interface de Rede:

```bash
ip -c a
```

Usando o IP acima, conecte-se via PuTTY em outro computador na mesma rede, usando o usuário criado no momento da instalação e defina senha root:

```bash
sudo passwd root
```

Logar como root com a senha criada acima:

```bash
su
```

### ⏰ Definir fuso horário para América/São_Paulo

```bash
timedatectl set-timezone America/Sao_Paulo
timedatectl  # Verificar se aplicou corretamente
```

<!-- Comentário: Adicionado em 08/04/2025 o comando para definir o fuso horário local. -->

---

## 1. Instalar Docker Engine e Docker Compose

📌 Fonte: https://docs.docker.com/engine/install/debian/

```bash
# Remover pacotes antigos
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Adicionar a chave GPG oficial do Docker
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Adicionar o repositório Docker
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

# Instalar Docker Engine e plugins
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Testar instalação
docker run hello-world
```

## 2. Instalar Docker Compose (standalone)

📌 Fonte: https://docs.docker.com/compose/install/standalone/

```bash
curl -SL https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker compose version
```

---

## 3. Instalar Pi-hole via Docker

### Criar rede `macvlan` para acesso direto na LAN

```bash
# Substituir eth0 conforme necessário (ver com `ip a`)
parent_iface=enx00e04c680094

# Criar rede macvlan para Pi-hole ter IP próprio
sudo docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=$parent_iface \
  pihole_macvlan
```

<!-- Comentário: Adicionado em 09/04/2025 rede macvlan para Pi-hole, essencial para funcionar como DHCP e ter IP fixo na LAN. -->

### Criar diretório para o Pi-hole:

```bash
mkdir -p /opt/homelab/pihole
cd /opt/homelab/pihole
```

### Criar `docker-compose.yml`:

```yaml
version: "3"

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    environment:
      TZ: "America/Sao_Paulo"
      WEBPASSWORD: ""
    volumes:
      - ./etc-pihole:/etc/pihole
      - ./etc-dnsmasq.d:/etc/dnsmasq.d
    restart: unless-stopped
    networks:
      pihole_macvlan:
        ipv4_address: 192.168.1.10

networks:
  pihole_macvlan:
    external: true
```

```bash
docker compose up -d
docker exec -it pihole pihole setpassword  # Definir senha via CLI (opcional)
```

---

## 4. Instalar Unbound via Docker

```bash
mkdir -p /opt/homelab/unbound
cd /opt/homelab/unbound
```

### Criar `unbound.conf`:

```conf
server:
  verbosity: 1
  interface: 0.0.0.0
  access-control: 0.0.0.0/0 allow
  do-ip4: yes
  do-udp: yes
  do-tcp: yes
  root-hints: "/etc/unbound/root.hints"
  harden-glue: yes
  harden-dnssec-stripped: yes
  use-caps-for-id: yes
  edns-buffer-size: 1232
  cache-min-ttl: 3600
  cache-max-ttl: 86400
  prefetch: yes
  num-threads: 2
  so-rcvbuf: 1m
  so-sndbuf: 1m

  unwanted-reply-threshold: 10000000
  rrset-roundrobin: yes

  hide-identity: yes
  hide-version: yes
  identity: "DNS"
  version: "1.0"

  qname-minimisation: yes
  minimal-responses: yes
```

### Criar `docker-compose.yml`:

```yaml
version: "3"

services:
  unbound:
    container_name: unbound
    image: mvance/unbound:latest
    restart: unless-stopped
    volumes:
      - ./unbound.conf:/etc/unbound/unbound.conf:ro
      - ./root.hints:/etc/unbound/root.hints:ro
    networks:
      - pihole_default

networks:
  pihole_default:
    external: true
```

```bash
docker compose up -d
```

---

## 5. Configurar o Pi-hole para usar o Unbound

Na interface web do Pi-hole em `http://192.168.1.10/admin`:

1. Acesse **Settings > DNS**
2. Em "Custom 1 (IPv4)", coloque:
   ```
   172.18.0.3#53
   ```
3. Desmarque todos os servidores DNS públicos
4. Clique em **Save**

---

## 🔐 Segurança e arquitetura

- Pi-hole com `macvlan` = visível na LAN com IP 192.168.1.10
- Unbound acessível **somente** via rede interna do Docker
- Pi-hole faz todo o DNS e DHCP (quando ativado), sem dependência de servidores externos
- Recomendação: manter firewall do host fechado (iptables ou UFW), exceto para DHCP e acesso ao painel web

---

## 🔍 Docker - Estado atual do ambiente

```bash
docker ps -a
docker images
docker network ls
docker network inspect pihole_macvlan
docker compose ls
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' pihole
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' unbound
dig @172.18.0.3 google.com
dig +trace google.com
dig +dnssec +multi dnssec-failed.org @172.18.0.3
```

---

