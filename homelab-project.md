# Projeto Homelab - Azzor1337x

Transformando um notebook antigo em um homelab funcional, moderno e enxuto 🚀

## 🖥️ Hardware

- Notebook com:
  - CPU: AMD C-60 APU with Radeon HD Graphics @ 1.0GHz
  - RAM: 8GB (2x4GB)
  - SSD: 120GB

## 🐧 Sistema Operacional

- **Debian 12** instalado com:
  - Webserver
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

**Estrutura do diretório:**

```text
~/homelab/blog/
├── docker-compose.yml
└── html/
    └── index.html
```

**Exemplo `docker-compose.yml`:**

```yaml
version: '3'

services:
  blog:
    image: nginx:alpine
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
    restart: unless-stopped
```

**Comando para subir:**

```bash
docker-compose up -d
```

Acesse localmente via: `http://localhost:8080`

---

### 4. Configurar Cloudflare Tunnel

#### 📌 Fonte: https://developers.cloudflare.com/cloudflared/

```bash
# Baixar e instalar cloudflared
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb

# Atualizar repositórios e instalar cloudflared
apt update
apt install cloudflared

# Autenticar com Cloudflare
cloudflared tunnel login

# Criar o túnel
cloudflared tunnel create homelab

# Criar o arquivo de configuração
nano /root/.cloudflared/config.yml
```

**Conteúdo de exemplo para `config.yml`:**

```yaml
tunnel: homelab
credentials-file: /root/.cloudflared/INFORMAR_AQUI_ID_DO_JSON.json

ingress:
  - hostname: azzor1337x.shop
    service: http://localhost:8080
  - service: http_status:404
```

⚠️ **Importante:** No Cloudflare DNS, adicione:

```
Tipo: CNAME
Nome: @
Conteúdo: SEU_ID_DO_TUNNEL.cfargotunnel.com
```

**Iniciar o túnel manualmente:**
```bash
cloudflared tunnel run homelab
```

**Instalar como serviço systemd:**
```bash
cloudflared service install
```

---

### 5. Adicionar Pi-hole + Unbound (em breve...)

### 6. Configurar Tailscale (em breve...)

### 7. Criar regras de firewall com nftables (em breve...)

### 8. Rodar o `btop` no terminal fixo (em breve...)

---

## 🔧 Extras

- Usar `screen` ou `tmux` para manter o `btop` ativo
- Backups e snapshots dos containers (opcional)
- Documentar mudanças neste arquivo conforme o projeto evolui

---

## 💡 Observações

- Todos os serviços serão containerizados para facilitar o gerenciamento e a reinstalação.
- O foco é manter tudo o mais leve e funcional possível.

