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

### 4. Instalar e configurar Unbound via Docker

> _📝 Reorganizado em 08/04/2025: Ordem ajustada para seguir o fluxo lógico de criação e validação do container._

#### 🧱 Estrutura de diretórios sugerida

```bash
mkdir -p /opt/homelab/unbound
cd /opt/homelab/unbound
```

#### 📝 Criar `unbound.conf`

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
  rrset-roundrobin: yes

forward-zone:
  name: "."
  forward-addr: 1.1.1.1
  forward-addr: 1.0.0.1
```

💬 Comentário: O bloco `forward-zone` pode ser removido se quiser usar resolução recursiva completa a partir da raiz (root DNS).

#### 📦 Criar `docker-compose.yml`

```bash
nano docker-compose.yml
```

**Conteúdo sugerido:**

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
    networks:
      - pihole_default

networks:
  pihole_default:
    external: true
```

#### 🚀 Subir o container

```bash
docker compose up -d
```

#### 🔍 Verificar IP do container `unbound`

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' unbound
```

#### 🧪 Testar resolução DNS usando `dig`

```bash
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

---

## Docker - Estado atual do ambiente

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
```

### Containers ativos:

```bash
CONTAINER ID   IMAGE                   COMMAND         STATUS                 NAMES
942e7d8d6eb2   mvance/unbound:latest   "/unbound.sh"   Up (healthy)          unbound
e97f8e2ebab4   pihole/pihole:latest    "start.sh"      Up (healthy)          pihole
```

### IPs dos containers na rede `pihole_default`:

- `pihole`: `172.18.0.2`
- `unbound`: `172.18.0.3`

