# Homelab Setup

## Projeto Homelab - Azzor1337x

Transformando um notebook antigo em um homelab funcional, moderno e enxuto 🚀

---

## 📑 Índice

- [🧭 Primeiros passos após instalação](#-primeiros-passos-após-instalação)
- [1. Docker Engine](#1-instalar-docker-engine-e-docker-compose)
- [2. Docker Compose standalone](#2-instalar-docker-compose-standalone)
- [3. Pi-hole](#3-instalar-pi-hole-via-docker)
- [4. Unbound](#4-instalar-unbound-via-docker)
- [5. Configurar Pi-hole com Unbound](#5-configurar-o-pi-hole-para-usar-o-unbound)
- [🔍 Docker - Estado atual do ambiente](#docker---estado-atual-do-ambiente)

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

### 3. Instalar Pi-hole via Docker

#### Criar diretório para o Pi-hole:

```bash
mkdir -p /opt/homelab/pihole
cd /opt/homelab/pihole
```

#### Criar arquivo de configuração do Docker Compose:

```bash
nano docker-compose.yml
```

**Conteúdo sugerido para `docker-compose.yml`:**

```yaml
# docker-compose.yml - Pi-hole
# Comentário: Substitua "WEBPASSWORD" por uma senha segura
version: "3"

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    environment:
      TZ: "America/Sao_Paulo"
      WEBPASSWORD: "sua_senha_aqui"
    volumes:
      - ./etc-pihole:/etc/pihole
      - ./etc-dnsmasq.d:/etc/dnsmasq.d
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "67:67/udp"
      - "80:80/tcp"
      - "443:443/tcp"
    restart: unless-stopped
```

#### Subir o container:

```bash
docker compose up -d
```

⚠️ **Aviso:** O Docker emitirá um aviso informando que a chave `version` no YAML está obsoleta. Isso é esperado e seguro ignorar por enquanto.

#### Alterar a senha do Pi-hole após o primeiro deploy (opcional e seguro):

```bash
docker exec -it pihole pihole setpassword
```

---

### 4. Instalar Unbound via Docker

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

# forward-zone removido para habilitar resolução recursiva direta (root DNS)
```

<!-- Comentário: Em 08/04/2025, removido o bloco `forward-zone` para habilitar resolução DNS recursiva autônoma usando os root servers. Também foi removida duplicação da diretiva rrset-roundrobin. -->

#### Criar o arquivo `docker-compose.yml`:

```bash
nano docker-compose.yml
```

```yaml
# docker-compose.yml - Unbound
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

### 🔄 Atualização automática diária do arquivo `root.hints`

```bash
crontab -e
```

Adicione ao final:

```cron
0 0 * * * curl -o /opt/homelab/unbound/root.hints https://www.internic.net/domain/named.cache
```

<!-- Comentário: Adicionado cron job diário às 00:00 para atualizar root.hints em 08/04/2025 -->

---

### 5. Configurar o Pi-hole para usar o Unbound

Acesse a interface web do Pi-hole em `http://<ip_do_homelab>/admin`:

1. Vá em **Settings > DNS**
2. Em "Custom 1 (IPv4)", coloque o IP do container `unbound`, por exemplo:
   
   ```text
   172.18.0.3#53
   ```
3. Desmarque todos os outros servidores DNS públicos (Cloudflare, Google, etc)
4. Clique em **Save**

Depois disso, o Pi-hole usará o Unbound como seu *resolver*, com resolução recursiva.

---

## 🔍 Docker - Estado atual do ambiente

> _📝 Adição comentada em 08/04/2025: Seção atualizada para incluir o container `unbound` e os comandos correspondentes._

### Comandos úteis para inspecionar o ambiente Docker:

```bash
# Lista todos os containers (em execução ou não)
docker ps -a

# Mostra todas as imagens disponíveis localmente
docker images

# Lista todas as redes Docker
docker network ls

# Inspeciona a rede 'pihole_default'
docker network inspect pihole_default

# Lista todos os volumes Docker (não estamos usando volumes no momento)
docker volume ls

# Lista todos os projetos Docker Compose detectados
docker compose ls

# Mostra o IP do container Pi-hole
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' pihole

# Mostra o IP do container Unbound
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' unbound

# Verifica resolução recursiva completa com rastreamento (útil para debug)
dig +trace google.com

# Verifica se DNSSEC está funcionando corretamente (espera-se SERVFAIL)
dig +dnssec +multi dnssec-failed.org @172.18.0.3

# Lista containers e seus status resumidos
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

<!-- Comentário: Em 08/04/2025, adicionados comandos para validação DNSSEC com dig e listagem resumida de containers com status/portas. -->

### Containers ativos:

```bash
CONTAINER ID   IMAGE                   COMMAND         STATUS                 NAMES
942e7d8d6eb2   mvance/unbound:latest   "/unbound.sh"   Up (healthy)          unbound
e97f8e2ebab4   pihole/pihole:latest    "start.sh"      Up (healthy)          pihole
```

### IPs dos containers na rede `pihole_default`:

- `pihole`: `172.18.0.2`
- `unbound`: `172.18.0.3`

### Teste de resolução DNS usando Unbound:

```bash
# Consulta DNS diretamente ao IP do container Unbound
dig @172.18.0.3 google.com
```

Exemplo de saída:

```text
;; ->>HEADER<<- opcode: QUERY, status: NOERROR
;; ANSWER SECTION:
google.com.   280   IN   A   172.217.29.46
;; SERVER: 172.18.0.3#53 (UDP)
```

🟢 Indica que o Unbound está respondendo corretamente.

