# 🧠 Homelab Setup

## 📚 Índice

1. [Primeiros passos após instalação](#-primeiros-passos-após-instalação)
2. [Instalar Docker Engine e Docker Compose](#1-instalar-docker-engine-e-docker-compose)
3. [Instalar Docker Compose (standalone)](#2-instalar-docker-compose-standalone)
4. [Instalar Pi-hole via Docker](#3-instalar-pi-hole-via-docker)
5. [Instalar Unbound via Docker](#4-instalar-unbound-via-docker)
6. [Configurar o Pi-hole para usar o Unbound](#5-configurar-o-pi-hole-para-usar-o-unbound)
7. [Docker - Estado atual do ambiente](#-docker---estado-atual-do-ambiente)
8. [Subir blog pessoal com Nginx](#-subir-blog-pessoal-com-nginx-exemplo-inicial-com-html-estático)
9. [Configurar Cloudflare Tunnel](#-configurar-cloudflare-tunnel)
10. [Planejamento: VPN no homelab (futuro)](#-planejamento-vpn-no-homelab-futuro)

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

### 1. Instalar Docker Engine e Docker Compose

#### 📌 Fonte: https://docs.docker.com/engine/install/debian/

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
sudo docker run hello-world
```

---

### 2. Instalar Docker Compose (standalone)

#### 📌 Fonte: https://docs.docker.com/compose/install/standalone/

```bash
# Baixar a versão standalone do Docker Compose
curl -SL https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

# Criar link simbólico
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

# Verificar versão instalada
docker compose version
```

---

### 3. Instalar Pi-hole via Docker

#### Criar diretório para o Pi-hole:

```bash
mkdir -p /opt/homelab/pihole
cd /opt/homelab/pihole
```

#### Criar arquivo de configuração do Docker Compose:

```bash
nano docker-compose.yml
```

**Conteúdo sugerido para `docker-compose.yml`:**

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
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "67:67/udp"
      - "80:80/tcp"
      - "443:443/tcp"
    restart: unless-stopped
```

#### Subir o container:

```bash
docker compose up -d
```

#### Alterar a senha do Pi-hole após o primeiro deploy (opcional e seguro):

```bash
docker exec -it pihole pihole setpassword
```

---

### 4. Instalar Unbound via Docker

#### Criar estrutura de diretórios:

```bash
mkdir -p /opt/homelab/unbound
cd /opt/homelab/unbound
```

#### Criar o arquivo `unbound.conf` com resolução recursiva (sem forwarders):

```bash
nano unbound.conf
```

**Conteúdo sugerido:**

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

<!-- Comentário: Em 08/04/2025, removido o bloco `forward-zone` para habilitar resolução DNS recursiva autônoma usando os root servers. Também foi removida duplicação da diretiva rrset-roundrobin. -->

#### Criar o arquivo `docker-compose.yml`:

```bash
nano docker-compose.yml
```

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

#### Subir o container:

```bash
docker compose up -d
```

---

### 🔄 Atualização automática diária do arquivo `root.hints`

```bash
crontab -e
```

Adicione ao final:

```cron
0 0 * * * curl -o /opt/homelab/unbound/root.hints https://www.internic.net/domain/named.cache
```

<!-- Comentário: Adicionado cron job diário às 00:00 para atualizar root.hints em 08/04/2025 -->

---

### 5. Configurar o Pi-hole para usar o Unbound

Acesse a interface web do Pi-hole em `http://<ip_do_homelab>/admin`:

1. Vá em **Settings > DNS**
2. Em "Custom 1 (IPv4)", coloque o IP do container `unbound`, por exemplo:

   ```text
   172.18.0.3#53
   ```
3. Desmarque todos os outros servidores DNS públicos (Cloudflare, Google, etc)
4. Clique em **Save**

Depois disso, o Pi-hole usará o Unbound como seu *resolver*, com resolução recursiva.

---

## 🔍 Docker - Estado atual do ambiente

### Comandos úteis para inspecionar o ambiente Docker:

```bash
docker ps -a
docker images
docker network ls
docker network inspect pihole_default
docker volume ls
docker compose ls
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' pihole
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' unbound
dig +trace google.com
dig +dnssec +multi dnssec-failed.org @172.18.0.3
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

### Teste de resolução DNS usando Unbound:

```bash
dig @172.18.0.3 google.com
```

---

<!-- Comentário: Adicionado em 08/04/2025 a configuração de Nginx com HTML estático, além do túnel via Cloudflare usando domínio personalizado. -->

### 🌐 Subir blog pessoal com Nginx (exemplo inicial com HTML estático)

#### 📁 Estrutura do diretório:

```text
/homelab/blog/
├── docker-compose.yml
└── html/
    └── index.html
```

#### 📜 Criar um index.html simples

```bash
mkdir -p /homelab/blog/html
nano /homelab/blog/html/index.html
```

##### Conteúdo do `index.html`

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

#### 📜 Criar um `docker-compose.yml`

```bash
nano /homelab/blog/docker-compose.yml
```

##### Conteúdo do `docker-compose.yml`

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

#### 🚀 Subir o container

```bash
cd /homelab/blog
chmod +x /usr/local/bin/docker-compose
docker-compose up -d
```

📌 Acesse o blog:

- Localmente via: http://localhost:8888  
- Externamente via Cloudflare Tunnel: https://azzor1337x.shop

---

### 🔒 Configurar Cloudflare Tunnel

#### 📌 Fonte: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/

#### 📥 Instalar `cloudflared`

```bash
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
rm cloudflared-linux-amd64.deb

apt update
apt install cloudflared
```

#### 🔐 Autenticar com Cloudflare

```bash
cloudflared tunnel login
```

#### 🛠️ Criar o túnel

```bash
cloudflared tunnel delete homelab
cloudflared tunnel create homelab  # ID será gerado automaticamente
```

#### 📁 Criar o arquivo de configuração

```bash
nano /root/.cloudflared/config.yml
```

##### Exemplo de `config.yml`

```yaml
tunnel: homelab
credentials-file: /root/.cloudflared/INSERIR_ID_DO_TUNNEL.json

ingress:
  - hostname: azzor1337x.shop
    service: http://127.0.0.1:8888
  - service: http_status:404
```

#### 🚀 Rodar o túnel manualmente

```bash
cloudflared tunnel run homelab
```

📌 **Importante:** Configure o DNS no painel da Cloudflare com um CNAME apontando `azzor1337x.shop` para `INSERIR_ID_DO_TUNNEL.cfargotunnel.com`.

#### 🧩 Instalar como serviço (systemd)

```bash
cloudflared service install
```

---

### 🧱 Planejamento: VPN no homelab (futuro)

- Objetivo: adicionar uma VPN segura ao homelab para acesso remoto seguro à rede local e aos serviços internos (ex: Pi-hole, blog, etc.)
- Prioridade: segurança, isolamento, simplicidade de configuração
- Opções consideradas:
  - WireGuard via Docker (simples, leve, rápido)
  - OpenVPN (mais tradicional, mas mais complexo)
  - Tailscale (mesh VPN com autenticação via conta; requer dependência externa)
- Integração planejada: Pi-hole poderá filtrar DNS para clientes VPN
- Ainda em análise se a VPN será containerizada ou instalada diretamente no host

<!-- Comentário: Em 08/04/2025 foi adicionada uma seção para planejamento futuro da VPN no homelab. Nenhuma VPN está em funcionamento neste momento. -->

