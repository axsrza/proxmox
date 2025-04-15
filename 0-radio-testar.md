# Configuração de Rádio Online com MPD + Icecast via Docker

Este documento descreve o setup de uma rádio online leve utilizando **MPD** e **Icecast** em containers Docker, transmitindo as músicas baixadas pela interface web do `metube`.

## Estrutura do Projeto

O projeto está localizado em `/srv/radio`:

```
/srv/radio
├── docker-compose.yml
├── mpd.conf
└── mpd/
    └── playlists/
```

## Passo a Passo

### 1. Criar a pasta do projeto

```bash
mkdir -p /srv/radio
cd /srv/radio
```

### 2. Criar o `docker-compose.yml`

```yaml
version: '3'
services:
  icecast:
    image: moul/icecast
    container_name: icecast
    ports:
      - "8887:8000"
    environment:
      - ICECAST_SOURCE_PASSWORD=hackme
      - ICECAST_ADMIN_PASSWORD=admin
      - ICECAST_RELAY_PASSWORD=relaypass
      - ICECAST_PASSWORD=hackme

  mpd:
    image: vimagick/mpd
    container_name: mpd
    depends_on:
      - icecast
    volumes:
      - /srv/metube/downloads:/music:ro
      - ./mpd:/var/lib/mpd
      - ./mpd.conf:/etc/mpd.conf
    ports:
      - "6600:6600"
```

### 3. Criar o arquivo `mpd.conf`

```conf
music_directory     "/music"
playlist_directory  "/var/lib/mpd/playlists"
db_file             "/var/lib/mpd/tag_cache"
log_file            "/var/lib/mpd/mpd.log"
pid_file            "/var/lib/mpd/mpd.pid"
state_file          "/var/lib/mpd/state"
sticker_file        "/var/lib/mpd/sticker.sql"

bind_to_address     "0.0.0.0"
port                "6600"

audio_output {
    type            "shout"
    name            "My Icecast Stream"
    host            "icecast"
    port            "8000"
    mount           "/stream"
    password        "hackme"
    format          "44100:16:2"
    encoding        "mp3"
    bitrate         "192"
}
```

### 4. Criar pastas necessárias

```bash
mkdir -p mpd/playlists
```

### 5. Subir os containers

```bash
docker compose up -d
```

## Acesso ao Stream

O Icecast estará acessível via:

```
http://radio.azzor1337x.shop:8887/
```

Link direto do stream:

```
http://radio.azzor1337x.shop:8887/stream
```

Esse link pode ser usado em players HTML5 ou apps como VLC.

## Notas

- O diretório `/srv/metube/downloads` deve conter os arquivos `.mp3`.
- A senha `hackme` pode ser alterada por segurança.
- O MPD pode ser controlado via `mpc` ou interfaces web externas, se desejado.

---

Para adicionar um player HTML na página, use:

```html
<audio controls autoplay>
  <source src="http://radio.azzor1337x.shop:8887/stream" type="audio/mpeg">
  Seu navegador não suporta o elemento de áudio.
</audio>
```

---

