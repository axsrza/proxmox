
---

```bash
sudo mkdir -p /opt/blog/encantare
sudo nano /opt/blog/encantare/index.html
```

```bash
<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Encantare Nails</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body, html {
      font-family: 'Helvetica Neue', sans-serif;
      color: #fff;
      background-color: #0e0e0e;
      scroll-behavior: smooth;
    }

    .hero-video {
      position: relative;
      height: 100vh;
      overflow: hidden;
    }

    .hero-video iframe {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 177.77vh; /* 100 * (16 / 9) */
      height: 100vh;
      transform: translate(-50%, -50%);
      pointer-events: none;
      z-index: 1;
    }

    .overlay {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.4);
      z-index: 2;
    }

    .hero-text {
      position: absolute;
      top: 50%;
      left: 50%;
      z-index: 3;
      transform: translate(-50%, -50%);
      text-align: center;
    }

    .hero-text h1 {
      font-size: 3em;
      letter-spacing: 4px;
      font-weight: 300;
    }

    .container {
      padding: 60px 20px;
      max-width: 1000px;
      margin: 0 auto;
    }

    .title {
      font-size: 2em;
      margin-bottom: 20px;
      color: #ffc0cb;
    }

    .section {
      margin-bottom: 60px;
    }

    .gallery {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 20px;
    }

    .gallery img {
      width: 100%;
      border-radius: 10px;
      object-fit: cover;
    }

    .buttons {
      display: flex;
      gap: 20px;
      justify-content: center;
      margin-top: 40px;
    }

    .btn {
      background: #ffc0cb;
      color: #000;
      padding: 15px 30px;
      border: none;
      border-radius: 30px;
      font-size: 1em;
      cursor: pointer;
      text-decoration: none;
      transition: all 0.3s ease;
    }

    .btn:hover {
      background: #fff;
      color: #000;
    }

    footer {
      background: #111;
      padding: 20px;
      text-align: center;
      font-size: 0.9em;
      color: #777;
    }

  </style>
</head>
<body>

  <div class="hero-video">
    <iframe src="https://player.vimeo.com/video/1076812298?muted=1&autoplay=1&autopause=0&loop=1&background=1&app_id=122963"
      frameborder="0"
      allow="autoplay; fullscreen"
      allowfullscreen>
    </iframe>
    <div class="overlay"></div>
    <div class="hero-text">
      <h1>Encantare Nails</h1>
    </div>
  </div>

  <div class="container">
    <div class="section">
      <h2 class="title">Beleza que Encanta</h2>
      <p>Especializadas em alongamento de unhas e esmaltação em gel, trazemos arte, cuidado e sofisticação para suas mãos.</p>
    </div>

    <div class="section">
      <h2 class="title">Galeria de Estilo</h2>
      <div class="gallery">
        <img src="https://i.pinimg.com/736x/af/4b/d0/af4bd023dbbe713d3197ecf8ef50fc10.jpg" alt="Unha 1"/>
        <img src="https://i.pinimg.com/736x/15/97/8f/15978f1cb815576340c4b31991aa8ea3.jpg" alt="Unha 3"/>
        <img src="https://i.pinimg.com/736x/f1/41/88/f141880276193c4c1435ed53026ecc9c.jpg" alt="Unha 4"/>
      </div>
    </div>

    <div class="section">
      <h2 class="title">Conecte-se Conosco</h2>
      <div class="buttons">
        <a class="btn" href="https://wa.me/seunumero" target="_blank">Agendar pelo WhatsApp</a>
        <a class="btn" href="https://instagram.com/seuinstagram" target="_blank">Ver no Instagram</a>
      </div>
    </div>
  </div>

  <footer>
    © 2025 Encantare Nails — Joinville/SC
  </footer>

</body>
</html>
```

```bash
sudo nano /opt/blog/docker-compose.yml
```

```bash
services:
  blog:
    image: nginx:alpine
    container_name: web_blog
    ports:
      - "8082:80"
    volumes:
      - .:/usr/share/nginx/html:ro
    restart: unless-stopped

  homelab:
    image: nginx:alpine
    container_name: web_homelab
    ports:
      - "8083:80"
    volumes:
      - ./homelab:/usr/share/nginx/html:ro
    restart: unless-stopped

  encantare:
    image: nginx:alpine
    container_name: web_encantare
    ports:
      - "8084:80"
    volumes:
      - ./encantare:/usr/share/nginx/html:ro
    restart: unless-stopped
```

```bash
cd /opt/blog
sudo docker-compose up -d
```

---
