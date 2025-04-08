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
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Projeto Homelab - Azzor1337x</title>
  <style>
    body {
      font-family: system-ui, sans-serif;
      margin: 0;
      padding: 2rem;
      background-color: #f5f5f5;
      color: #333;
    }
    h1, h2, h3 {
      color: #111;
    }
    code, pre {
      background-color: #eee;
      padding: 0.4em 0.6em;
      border-radius: 5px;
      font-family: monospace;
      display: block;
      overflow-x: auto;
      white-space: pre-wrap;
    }
    a {
      color: #0366d6;
      text-decoration: none;
    }
    a:hover {
      text-decoration: underline;
    }
    section {
      margin-bottom: 3rem;
    }
    hr {
      border: none;
      height: 1px;
      background: #ccc;
      margin: 2rem 0;
    }
  </style>
</head>
<body>

<h1>📓 Projeto Homelab - Azzor1337x</h1>
<p>Transformando um notebook antigo em um homelab funcional, moderno e enxuto 🚀</p>

<hr/>

<section>
<h2>🧭 Primeiros passos após instalação</h2>
<p>Após finalizar a instalação do Debian 12 minimal, no terminal local logar com o usuário criado, verifique o IP e Interface de Rede:</p>
<pre><code>ip -c a</code></pre>

<p>Usando o IP acima, conecte-se via PuTTY em outro computador na mesma rede, usando o usuário criado no momento da instalação e defina senha root:</p>
<pre><code>sudo passwd root</code></pre>

<p>Logar como root com a senha criada acima:</p>
<pre><code>su</code></pre>
</section>

<hr/>

<section>
<h2>🖥️ Hardware</h2>
<ul>
  <li>Notebook com:
    <ul>
      <li>CPU: AMD C-60 APU with Radeon HD Graphics @ 1.0GHz</li>
      <li>RAM: 8GB (2x4GB)</li>
      <li>SSD: 120GB</li>
    </ul>
  </li>
</ul>

<h2>🐧 Sistema Operacional</h2>
<ul>
  <li><strong>Debian 12</strong> instalado com:
    <ul>
      <li>SSH Server</li>
      <li>Standard System Utilities</li>
    </ul>
  </li>
</ul>
</section>

<hr/>

<section>
<h2>🧠 Objetivo</h2>
<p>Criar um homelab funcional com os seguintes serviços:</p>
<ul>
  <li>[ ] DNS local com <strong>Unbound</strong></li>
  <li>[ ] Bloqueador de anúncios com <strong>Pi-hole</strong></li>
  <li>[ ] Servidor DHCP local (integrado com Pi-hole)</li>
  <li>[ ] Blog pessoal online em <strong>azzor1337x.shop</strong></li>
  <li>[ ] Exposição segura via <strong>Cloudflare Tunnel</strong></li>
  <li>[ ] Acesso remoto via <strong>Tailscale</strong></li>
  <li>[ ] Monitoramento local com <strong>btop</strong></li>
  <li>[ ] Firewall moderno com <strong>nftables</strong></li>
  <li>[ ] Containerização de tudo via <strong>Docker + Docker Compose</strong></li>
</ul>
</section>

<hr/>

<section>
<h2>📦 Etapas do Projeto</h2>

<h3>1. Instalar Docker Engine e Docker Compose</h3>
<p><a href="https://docs.docker.com/engine/install/debian/">Fonte oficial</a></p>
<pre><code># Remover pacotes antigos
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Adicionar GPG do Docker
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Adicionar repositório Docker
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo \"$VERSION_CODENAME\") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Testar
sudo docker run hello-world</code></pre>

<h3>2. Instalar Docker Compose (standalone)</h3>
<p><a href="https://docs.docker.com/compose/install/standalone/">Fonte oficial</a></p>
<pre><code>curl -SL https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker compose version</code></pre>

<h3>3. Subir blog pessoal com Nginx</h3>
<pre><code>/homelab/blog/
├── docker-compose.yml
└── html/
    └── index.html</code></pre>

<p>Criar arquivos:</p>
<pre><code>mkdir -p /homelab/blog/html
nano /homelab/blog/html/index.html</code></pre>

<p>Conteúdo do index.html:</p>
<pre><code>&lt;!DOCTYPE html&gt;
&lt;html lang="pt-BR"&gt;
&lt;head&gt;&lt;meta charset="UTF-8"&gt;&lt;title&gt;Blog Azzor1337x&lt;/title&gt;&lt;/head&gt;
&lt;body&gt;&lt;h1&gt;Bem-vindo ao meu blog!&lt;/h1&gt;&lt;p&gt;Este é o início do meu homelab! 🚀&lt;/p&gt;&lt;/body&gt;
&lt;/html&gt;
</code></pre>

<p>Docker Compose:</p>
<pre><code>nano /homelab/blog/docker-compose.yml</code></pre>

<pre><code>version: '3'
services:
  blog:
    image: nginx:alpine
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
    restart: unless-stopped
</code></pre>

<p>Subir serviço:</p>
<pre><code>cd /homelab/blog
chmod +x /usr/local/bin/docker-compose
docker-compose up -d</code></pre>
<p>Acesse: <a href="http://localhost:8080">http://localhost:8080</a></p>

<h3>4. Configurar Cloudflare Tunnel</h3>
<p><a href="https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/">Fonte oficial</a></p>
<pre><code>wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
rm cloudflared-linux-amd64.deb

apt update
apt install cloudflared
cloudflared tunnel login
cloudflared tunnel delete homelab
cloudflared tunnel create homelab</code></pre>

<p>Arquivo config:</p>
<pre><code>nano /root/.cloudflared/config.yml</code></pre>

<pre><code>tunnel: homelab
credentials-file: /root/.cloudflared/INSERIR_ID_DO_TUNNEL.json
ingress:
  - hostname: azzor1337x.shop
    service: http://localhost:8080
  - service: http_status:404</code></pre>

<pre><code>cloudflared tunnel run homelab
cloudflared service install</code></pre>
</section>

<hr/>

<section>
<h3>🔒 Extra: Rodar containers com usuário não-root (futuro)</h3>
<p>Em breve, containers rodando fora do root para mais segurança.</p>

<h3>🔧 Extras</h3>
<ul>
  <li>Usar <code>screen</code> ou <code>tmux</code> com <code>btop</code></li>
  <li>Backups e snapshots dos containers</li>
  <li>Documentar mudanças no projeto</li>
</ul>

<h3>💡 Observações</h3>
<ul>
  <li>Todos os serviços serão containerizados</li>
  <li>Foco: leveza, segurança e praticidade</li>
</ul>
</section>

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

### 🔒 Extra: Rodar containers com usuário não-root (futuro)

Em uma etapa futura, será possível adaptar o projeto para rodar os serviços com um usuário não-root, com todos os arquivos e volumes dentro de uma home isolada, aumentando a segurança e isolando ainda mais o host.

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

