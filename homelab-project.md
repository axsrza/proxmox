# Projeto Homelab - Azzor1337x

Transformando um notebook antigo em um homelab funcional, moderno e enxuto 🚀

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

---

## 🖥️ Hardware

- Notebook com:
  - CPU: AMD C-60 APU with Radeon HD Graphics @ 1.0GHz
  - RAM: 8GB (2x4GB)
  - SSD: 120GB

## 🐧 Sistema Operacional

- **Debian 12** instalado com:
  - SSH Server
  - Standard System Utilities

---

## 🧠 Objetivo

Criar um homelab funcional com os seguintes serviços:

- [ ] DNS local com **Unbound**
- [ ] Bloqueador de anúncios com **Pi-hole**
- [ ] Servidor DHCP local (integrado com Pi-hole)
- [ ] Blog pessoal online em **azzor1337x.shop**
- [ ] Exposição segura via **Cloudflare Tunnel**
- [ ] Acesso remoto via **Tailscale**
- [ ] Monitoramento local com **btop**
- [ ] Firewall moderno com **nftables**
- [ ] Containerização de tudo via **Docker + Docker Compose**

---

## 📦 Etapas do Projeto

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

### 3. Subir blog pessoal com Nginx (exemplo inicial com HTML estático)

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

#### 📜 Exemplo `docker-compose.yml`

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

#### 🚀 Comando para subir:

```bash
cd /homelab/blog
chmod +x /usr/local/bin/docker-compose
docker-compose up -d
```

Acesse localmente via: `http://localhost:8888`

---

### 4. Configurar Cloudflare Tunnel

#### 📌 Fonte: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/

#### 📜 Instalar Cloudflared

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

#### 🛠️ Criar o tunnel

```bash
cloudflared tunnel delete homelab
cloudflared tunnel create homelab #ID_DO_TUNNEL
```

#### 📁 Criar o arquivo de configuração:

```bash
nano /root/.cloudflared/config.yml
```

```yaml
tunnel: homelab
credentials-file: /root/.cloudflared/INSERIR_ID_DO_TUNNEL.json

ingress:
  - hostname: azzor1337x.shop
    service: http://127.0.0.1:8888
  - service: http_status:404
```

#### 🚀 Rodar o tunnel (dash.cloudflare.com CNAME @ INSERIR_ID_DO_TUNNEL.cfargotunnel.com)

```bash
cloudflared tunnel run homelab
```

#### 💡 Instalar como serviço systemd

```bash
cloudflared service install
```

---

# 5. Adicionar Pi-hole + Unbound via Docker

## 🧠 Objetivo
Configurar o Pi-hole como bloqueador de anúncios e resolver DNS localmente com Unbound, tudo containerizado via Docker.

---

## 📁 Estrutura do diretório

```bash
/homelab/pihole-unbound/
├── docker-compose.yml
├── etc-pihole/             # persistência do Pi-hole
├── etc-dnsmasq.d/          # configurações extras do Pi-hole
└── unbound/
    └── unbound.conf        # config personalizada do Unbound
```

---

## 🔧 Criar diretórios e arquivos base

```bash
mkdir -p /homelab/pihole-unbound/{etc-pihole,etc-dnsmasq.d,unbound}
nano /homelab/pihole-unbound/unbound/unbound.conf
```

### 📜 Exemplo: `unbound.conf`

```conf
a server:
    verbosity: 1
    interface: 0.0.0.0
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes
    access-control: 0.0.0.0/0 allow
    root-hints: "/etc/unbound/root.hints"
    harden-glue: yes
    harden-dnssec-stripped: yes
    use-caps-for-id: no
    edns-buffer-size: 1232
    prefetch: yes
    cache-min-ttl: 3600
    cache-max-ttl: 86400
    hide-identity: yes
    hide-version: yes
    qname-minimisation: yes
    rrset-roundrobin: yes
    so-rcvbuf: 1m
    so-sndbuf: 1m
    val-clean-additional: yes

forward-zone:
    name: "."
    forward-addr: 1.1.1.1
    forward-addr: 1.0.0.1
```

---

## 📦 Criar `docker-compose.yml`

```bash
nano /homelab/pihole-unbound/docker-compose.yml
```

### 📜 Conteúdo:

```yaml
version: "3"

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "81:80/tcp"
    environment:
      TZ: "America/Sao_Paulo"
      WEBPASSWORD: "senhaforte"
      DNS1: 127.0.0.1#5335
      DNS2: 127.0.0.1#5335
    volumes:
      - ./etc-pihole/:/etc/pihole/
      - ./etc-dnsmasq.d/:/etc/dnsmasq.d/
    restart: unless-stopped

  unbound:
    container_name: unbound
    image: mvance/unbound:latest
    ports:
      - "5335:5335/tcp"
      - "5335:5335/udp"
    volumes:
      - ./unbound:/opt/unbound/etc/unbound
    restart: unless-stopped
```

---

## 🚀 Subir os containers

```bash
cd /homelab/pihole-unbound
docker-compose up -d
```

Acesse a interface do Pi-hole: `http://localhost:81`

---

## 🔍 Testar se o Unbound está funcionando

```bash
dig @127.0.0.1 -p 5335 google.com
```

A resposta deve mostrar o tempo e que o servidor usado foi `127.0.0.1#5335`.

---

## 🔒 Configurações extras (futuro)
- Integrar DHCP local pelo Pi-hole
- Integrar com Tailscale (resolução via Tailscale DNS)
- Rodar com usuário não-root (com `puid`/`pgid` via docker-compose)

---

## 💡 Observações
- O Pi-hole e Unbound estão separados, mas integrados via Docker
- A porta `81` foi escolhida para não conflitar com o blog (porta `8888`)
- Tudo roda em containers isolados, persistência ativada

