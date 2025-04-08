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

# 5. Adicionar Pi-hole + Unbound (via Docker)

## Visão geral
Essa etapa cobre a instalação e configuração do Pi-hole como servidor DNS com bloqueio de anúncios e do Unbound como DNS recursivo local (resolver seguro e privativo). Ambos serão executados em containers Docker, com o Unbound atendendo localmente o Pi-hole como resolver upstream. O objetivo é garantir que:

- O **Pi-hole** responda por requisições DNS da rede e bloqueie domínios maliciosos ou de propaganda.
- O **Unbound** faça as consultas DNS diretamente aos servidores raiz, sem recorrer a DNS públicos como 1.1.1.1 ou 8.8.8.8.
- A **porta 80** do host esteja dedicada ao Pi-hole (interface web), já que o blog está na porta 8888.

---

## Estrutura de diretórios esperada
Criaremos a seguinte estrutura para os arquivos de configuração e volumes persistentes:

```bash
mkdir -p ~/docker/pihole-unbound/{unbound,etc-pihole,etc-dnsmasq.d}
```

Estrutura resultante:
```
~/docker/pihole-unbound/
├── docker-compose.yml
├── unbound/
│   └── unbound.conf
├── etc-pihole/        # Persistência dos dados do Pi-hole
├── etc-dnsmasq.d/     # Config do dnsmasq (usado pelo Pi-hole)
```

---

## 1. Criar o arquivo `unbound.conf`

Criar em `~/docker/pihole-unbound/unbound/unbound.conf` com o conteúdo:

```bash
nano ~/docker/pihole-unbound/unbound/unbound.conf
```

```conf
server:
  verbosity: 1
  interface: 0.0.0.0
  port: 5335
  do-ip4: yes
  do-udp: yes
  do-tcp: yes
  hide-identity: yes
  hide-version: yes
  use-caps-for-id: yes
  edns-buffer-size: 1232
  harden-glue: yes
  harden-dnssec-stripped: yes
  cache-min-ttl: 3600
  cache-max-ttl: 86400
  prefetch: yes
  num-threads: 2
  so-rcvbuf: 4m
  so-sndbuf: 4m
  val-log-level: 1
  unwanted-reply-threshold: 10000

  rrset-roundrobin: yes

  trust-anchor-file: "/var/lib/unbound/root.key"

  root-hints: "/var/lib/unbound/root.hints"

forward-zone:
  name: "."
  forward-tls-upstream: no
```

---

## 2. Criar `docker-compose.yml`

Criar em `~/docker/pihole-unbound/docker-compose.yml`:

```bash
nano ~/docker/pihole-unbound/docker-compose.yml
```

```yaml
version: "3"

services:
  unbound:
    image: mvance/unbound:latest
    container_name: unbound
    restart: unless-stopped
    volumes:
      - ./unbound:/opt/unbound/etc/unbound
    ports:
      - "5335:5335/tcp"
      - "5335:5335/udp"
    networks:
      - dns_net

  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    restart: unless-stopped
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "80:80"  # Interface web do Pi-hole
    environment:
      TZ: "America/Sao_Paulo"
      WEBPASSWORD: "sua_senha_aqui"
      DNS1: 127.0.0.1#5335
      DNS2: 127.0.0.1#5335
    volumes:
      - ./etc-pihole/:/etc/pihole/
      - ./etc-dnsmasq.d/:/etc/dnsmasq.d/
    networks:
      - dns_net
    cap_add:
      - NET_ADMIN

networks:
  dns_net:
    driver: bridge
```

---

## 3. Iniciar os containers

```bash
cd ~/docker/pihole-unbound
sudo docker compose up -d
```

---

## 4. Verificar funcionamento

- Interface web do Pi-hole: `http://localhost/admin`
- DNS do Pi-hole: porta 53 (TCP/UDP)
- DNS do Unbound: porta 5335 (TCP/UDP)

No painel do Pi-hole, vá em **Settings > DNS** e verifique que o resolver configurado é:
```
127.0.0.1#5335
```

Assim, todas as consultas da rede passam pelo Pi-hole e são resolvidas de forma segura e direta pelo Unbound (sem usar 8.8.8.8 ou 1.1.1.1).

---

## 5. Testar resolução DNS

```bash
nslookup google.com 127.0.0.1
```

---

✅ Finalizado. Seu Pi-hole está rodando com Unbound local como DNS recursivo seguro. O sistema está pronto para assumir o papel de servidor DNS da sua rede!

> 💡 **Dica:** Agora você pode configurar seu roteador (ou DHCP) para apontar o IP do seu homelab como servidor DNS principal.

