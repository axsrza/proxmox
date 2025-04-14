# 🌐 Homelab: Servidor Web e Cloudflare Tunnel com Subdomínios

## 📁 Estrutura de Pastas

```bash
mkdir -p /srv/blog
mkdir -p /srv/blog/homelab
mkdir -p /srv/navidrome/data
mkdir -p /srv/metube/downloads
```

## 📝 HTML para Página Principal (blog) e Subdomínio homelab

Crie os arquivos HTML:

```bash
nano /srv/blog/index.html
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
  <p>blog!</p>
</body>
</html>
```

```bash
nano /srv/blog/homelab/index.html
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
  <p>homelab!</p>
</body>
</html>
```

## 🐳 docker-compose para Nginx (index e homelab)

```bash
nano /srv/blog/docker-compose.yml
```

```yaml
services:
  blog:
    image: nginx:alpine
    container_name: web_blog
    ports:
      - "8888:80"
    volumes:
      - .:/usr/share/nginx/html:ro
    restart: unless-stopped

  homelab:
    image: nginx:alpine
    container_name: web_homelab
    ports:
      - "8889:80"
    volumes:
      - ./homelab:/usr/share/nginx/html:ro
    restart: unless-stopped
```

```bash
cd /srv/blog
sudo docker-compose up -d
```

---

## 🎵 docker-compose para Navidrome

```bash
nano /srv/navidrome/docker-compose.yml
```

```yaml
services:
  navidrome:
    image: deluan/navidrome:latest
    container_name: navidrome
    ports:
      - "4533:4533"
    volumes:
      - /srv/navidrome/data:/data
      - /srv/metube/downloads:/music
    environment:
      ND_SCANINTERVAL: 1m
      ND_LOGLEVEL: info
    restart: unless-stopped
```

```bash
cd /srv/navidrome
sudo docker-compose up -d
```

---

## ☁️ Cloudflared Tunnel

### Instalar Cloudflared

```bash
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
rm cloudflared-linux-amd64.deb
```

### Autenticar

```bash
cloudflared tunnel login
```

### Criar o túnel

```bash
cloudflared tunnel create blog
```

Copie o nome do túnel (UUID).

---

## ⚙️ Configuração do Túnel

```bash
nano /root/.cloudflared/config.yml
```

```yaml
tunnel: blog
credentials-file: /root/.cloudflared/UUID_DO_TUNNEL.json

ingress:
  - hostname: azzor1337x.shop
    service: http://127.0.0.1:8888

  - hostname: homelab.azzor1337x.shop
    service: http://127.0.0.1:8889

  - hostname: music.azzor1337x.shop
    service: http://127.0.0.1:4533

  - service: http_status:404
```

---

## 🌐 Entradas DNS na Cloudflare

Em `https://dash.cloudflare.com`:

- Tipo: `CNAME`
- Nome: `@` → Valor: `UUID.tunnel.cloudflare.com`
- Nome: `homelab` → Valor: `UUID.tunnel.cloudflare.com`
- Nome: `music` → Valor: `UUID.tunnel.cloudflare.com`

⚠️ Substitua `UUID` pelo ID real do túnel criado.

---

## 🚀 Iniciar o túnel

```bash
cloudflared tunnel run blog
```

Ou instale como serviço:

```bash
cloudflared service install
```

---

## ✅ Testes

- https://azzor1337x.shop → Página principal (blog)
- https://homelab.azzor1337x.shop → Página do homelab
- https://music.azzor1337x.shop → Navidrome

