# Homelab Setup

## 📋 Índice

- [🧭 Primeiros passos após instalação](#-primeiros-passos-ap%C3%B3s-instala%C3%A7%C3%A3o)
- [🐳 Instalação do Docker e Docker Compose](#1-instalar-docker-engine-e-docker-compose)
- [📦 Instalar o Pi-hole via Docker](#3-instalar-pi-hole-via-docker)
- [🔐 Instalar o Unbound via Docker](#4-instalar-unbound-via-docker)
- [🔗 Conectar Pi-hole ao Unbound](#5-configurar-o-pi-hole-para-usar-o-unbound)
- [🔍 Estado atual do ambiente Docker](#-docker---estado-atual-do-ambiente)

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

## 1. Instalar Docker Engine e Docker Compose

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

## 2. Instalar Docker Compose (standalone)

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

## 3. Instalar Pi-hole via Docker

#### Criar diretório para o Pi-hole:

```bash
mkdir -p /opt/homelab/pihole
cd /opt/homelab/pihole
```

#### Criar arquivo de configuração do Docker Compose:

```bash
nano docker-compose.yml
```

**Conteúdo sugerido para `docker-compose.yml` com `macvlan`:**

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
    restart: unless-stopped
    networks:
      macvlan_net:
        ipv4_address: 192.168.1.99

networks:
  macvlan_net:
    driver: macvlan
    driver_opts:
      parent: enx00e04c680094  # Interface física detectada com `ip a`
    ipam:
      config:
        - subnet: 192.168.1.0/24
          gateway: 192.168.1.1
```

<!-- Comentário: Em 09/04/2025, atualizado para utilizar rede macvlan e IP fixo 192.168.1.99 no container Pi-hole. -->

#### Subir o container:

```bash
docker compose up -d
```

#### Alterar a senha do Pi-hole após o primeiro deploy (opcional e seguro):

```bash
docker exec -it pihole pihole setpassword
```

---

## 4. Instalar Unbound via Docker

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

<!-- Comentário: Em 08/04/2025, removido o bloco `forward-zone` para habilitar resolução DNS recursiva autônoma usando os root servers. -->

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

## 5. Configurar o Pi-hole para usar o Unbound

Acesse a interface web do Pi-hole em `http://192.168.1.99/admin`:

1. Vá em **Settings > DNS**
2. Em "Custom 1 (IPv4)", coloque o IP do container `unbound`, por exemplo:

   ```text
   172.18.0.2#53
   ```
3. Desmarque todos os outros servidores DNS públicos (Cloudflare, Google, etc)
4. Clique em **Save**

Depois disso, o Pi-hole usará o Unbound como seu *resolver*, com resolução recursiva.

<!-- Comentário: Em 09/04/2025, ajustado IP do Unbound para 172.18.0.2 após inspeção manual do container. -->

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
dig +dnssec +multi dnssec-failed.org @172.18.0.2
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

### Teste de resolução DNS usando Unbound:

```bash
dig @172.18.0.2 google.com
```

<!-- Comentário: Em 09/04/2025, atualizado endereço do Unbound para 172.18.0.2. -->

---

## 💡 Observação sobre DHCP

Quando o Pi-hole for ativado como servidor DHCP, ele **não muda o próprio IP** configurado no `docker-compose.yml`. O IP fixo 192.168.1.99 continuará sendo utilizado, mesmo com o serviço DHCP ativo.

<!-- Comentário: Esclarecido em 09/04/2025 que o Pi-hole com IP fixo via `macvlan` não perde o IP ao assumir papel de servidor DHCP. -->

