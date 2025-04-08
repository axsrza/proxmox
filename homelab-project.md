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

# 5. Adicionar Pi-hole + Unbound (Docker)

## Objetivo
Instalar Pi-hole (bloqueador de anúncios e rastreadores) com Unbound (resolvedor DNS recursivo) em containers Docker separados, de forma que o Pi-hole use o Unbound como DNS interno e faça consultas diretamente à raiz da internet (sem depender de Google, Cloudflare, etc.).

## Estrutura de Pastas
```bash
# Criar estrutura de diretórios personalizada para o serviço DNS no homelab
sudo mkdir -p /homelab/dns/unbound
sudo mkdir -p /homelab/dns/etc-pihole
sudo mkdir -p /homelab/dns/etc-dnsmasq.d
```

## Configuração do Unbound
Crie o arquivo de configuração principal do Unbound:
```bash
sudo nano /homelab/dns/unbound/unbound.conf
```

Conteúdo do `unbound.conf`:
```conf
server:
  verbosity: 0
  interface: 0.0.0.0
  port: 5335
  do-ip4: yes
  do-udp: yes
  do-tcp: yes
  root-hints: "/etc/unbound/root.hints"
  harden-glue: yes
  harden-dnssec-stripped: yes
  use-caps-for-id: no
  edns-buffer-size: 1232
  cache-min-ttl: 3600
  cache-max-ttl: 86400
  prefetch: yes
  qname-minimisation: yes
  hide-identity: yes
  hide-version: yes
  do-not-query-localhost: no

forward-zone:
  name: "."
  # Consulta direta à raiz DNS usando root.hints
  forward-addr: 198.41.0.4       # a.root-servers.net
  forward-addr: 199.9.14.201     # b.root-servers.net
  forward-addr: 192.33.4.12      # c.root-servers.net
  forward-addr: 199.7.91.13      # d.root-servers.net
```

> 💡 Dica: mantenha seu arquivo `root.hints` atualizado:
```bash
wget -O /homelab/dns/unbound/root.hints https://www.internic.net/domain/named.root
```

## docker-compose.yml
Crie o `docker-compose.yml`:
```bash
sudo nano /homelab/dns/docker-compose.yml
```

Conteúdo:
```yaml
version: "3"

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    hostname: pihole
    ports:
      - "80:80"       # Web Admin
      - "53:53/tcp"   # DNS TCP
      - "53:53/udp"   # DNS UDP
    environment:
      TZ: "America/Sao_Paulo"
      WEBPASSWORD: "sua_senha_aqui"
      DNS1: 127.0.0.1#5335
      DNS2: 127.0.0.1#5335
    volumes:
      - /homelab/dns/etc-pihole/:/etc/pihole/
      - /homelab/dns/etc-dnsmasq.d/:/etc/dnsmasq.d/
    restart: unless-stopped
    depends_on:
      - unbound

  unbound:
    container_name: unbound
    image: mvance/unbound:latest
    ports:
      - "5335:5335/udp"
      - "5335:5335/tcp"
    volumes:
      - /homelab/dns/unbound:/opt/unbound/etc/unbound
    restart: unless-stopped
```

## Redes e Portas
- **Pi-hole**:
  - Porta 80 → Interface web
  - Porta 53 (TCP/UDP) → DNS
- **Unbound**:
  - Porta 5335 (TCP/UDP) → DNS recursivo local

As portas foram definidas dessa forma para evitar conflitos com o blog que agora roda na porta `8888`, liberando a **porta 80** para o Pi-hole.

A rede padrão usada é `bridge`, o que permite comunicação entre os containers e com o host. Não usamos `host` para manter o isolamento e controle fino de portas.

## Inicializar o serviço
```bash
cd /homelab/dns
sudo docker compose up -d
```

Acesse a interface do Pi-hole:
```
http://localhost
```

## Testar resolução DNS com Unbound
```bash
dig google.com @127.0.0.1 -p 5335
```

## Resumo
✅ Pi-hole roda na porta 80 (livre após mudança do blog para 8888)  
✅ Unbound consulta diretamente os root servers via `root.hints`  
✅ Estrutura organizada em `/homelab/dns`  
✅ Containers separados, usando rede bridge  
✅ DNS 100% independente de serviços como Google ou Cloudflare

