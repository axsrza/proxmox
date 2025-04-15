## 🌐 Servidor Web com Subdomínios via Nginx Reverso + Cloudflare Tunnel

### 🎯 Objetivo
Acessar:
- `blog.azzor1337x.shop` → blog HTML simples
- `music.azzor1337x.shop` → Navidrome player de música

Tudo isso em um servidor local usando:
- Docker
- Nginx como proxy reverso
- Túnel Cloudflare apontando `azzor1337x.shop` para o IP local

---

### 📁 Estrutura de diretórios

```
/srv/
├── nginx-reverse/
│   ├── docker-compose.yml
│   ├── nginx/
│   │   └── conf.d/
│   │       └── default.conf
│   └── blog/
│       └── index.html
└── navidrome/
    ├── data/
    └── docker-compose.yml
```

---

### 🔧 NGINX - Reverse Proxy

**/srv/nginx-reverse/docker-compose.yml**

```yaml
version: '3'

services:
  nginx:
    image: nginx:alpine
    container_name: nginx-reverse
    ports:
      - "80:80"
    volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
      - ./blog:/usr/share/nginx/html:ro
    networks:
      - nginxnet
    restart: unless-stopped

networks:
  nginxnet:
    external: true
```

---

**/srv/nginx-reverse/nginx/conf.d/default.conf**

```nginx
server {
    listen 80;
    server_name blog.azzor1337x.shop;

    location / {
        root /usr/share/nginx/html;
        index index.html;
    }
}

server {
    listen 80;
    server_name music.azzor1337x.shop;

    location / {
        proxy_pass http://navidrome:4533;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

---

### 📝 Blog HTML simples

**/srv/nginx-reverse/blog/index.html**

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

---

### 🎵 Navidrome

**/srv/navidrome/docker-compose.yml**

```yaml
version: '3'

services:
  navidrome:
    image: deluan/navidrome:latest
    container_name: navidrome
    volumes:
      - /srv/navidrome/data:/data
      - /srv/metube/downloads:/music
    environment:
      ND_SCANINTERVAL: 1m
      ND_LOGLEVEL: info
    networks:
      - nginxnet
    restart: unless-stopped

networks:
  nginxnet:
    external: true
```

---

### 🛠️ Comandos para rodar tudo

```bash
# Crie rede externa para compartilhar entre nginx e navidrome
sudo docker network create nginxnet

# Suba o nginx reverso
cd /srv/nginx-reverse
sudo docker-compose up -d

# Suba o Navidrome
cd /srv/navidrome
sudo docker-compose up -d
```

---

### ☁️ Cloudflare Tunnel

No painel da Cloudflare ou com `cloudflared`:

- `blog.azzor1337x.shop` → http://localhost:80
- `music.azzor1337x.shop` → http://localhost:80

---

### ✅ Testes finais

Acesse no navegador:
- [blog.azzor1337x.shop](http://blog.azzor1337x.shop)
- [music.azzor1337x.shop](http://music.azzor1337x.shop)

---

### 📌 Obs:
- O Nginx está escutando na porta 80 e direcionando conforme o subdomínio.
- Você pode adicionar mais serviços no futuro só criando novos `server` blocks no Nginx.

---

🎉 Agora seu homelab tem um blog e um player de música acessíveis via subdomínios elegantes!

