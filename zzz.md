
# Homelab - Pi-hole + Unbound

## Descrição
Este tutorial configura um homelab utilizando um notebook antigo com o processador AMD C-60 APU, 8 GB de RAM e um SSD de 120 GB. O sistema operacional utilizado é o Debian 12 minimal. O objetivo é configurar o Pi-hole com Unbound utilizando Docker.

## Estrutura de Diretórios

A estrutura de diretórios para o homelab será organizada da seguinte forma:

```
/home/homelab/
│
├───pihole-unbound/
│   ├───docker-compose.yml
│
└───pihole-unbound-data/
    ├───pihole/
    │   ├───etc-pihole/        # Dados persistentes do Pi-hole
    │   └───etc-dnsmasq.d/     # Configurações do DNSMasq
    │
    └───unbound/               # (Opcional) Dados persistentes do Unbound
        └───etc-unbound/
```

## Passos de Configuração

### 1. Preparação do Sistema
Logue como root e configure o sistema:

```bash
ip -c a
```

```bash
sudo passwd root
```

```bash
su
```

Configuração de timezone e remoção de pacotes antigos:

```bash
timedatectl set-timezone America/Sao_Paulo
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian   $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
curl -SL https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker compose version
```

### 2. Criar os Diretórios para o Pi-hole e Unbound

Crie os diretórios diretamente no seu home:

```bash
mkdir -p ~/pihole-unbound
mkdir -p ~/pihole-unbound-data/pihole/etc-pihole
mkdir -p ~/pihole-unbound-data/pihole/etc-dnsmasq.d
mkdir -p ~/pihole-unbound-data/unbound/etc-unbound
cd ~/pihole-unbound
```

### 3. Criar o arquivo `docker-compose.yml`

Edite o arquivo `docker-compose.yml` para configurar os containers:

```bash
nano docker-compose.yml
```

Adicione o conteúdo abaixo no arquivo `docker-compose.yml`:

```yaml
version: "3"

services:
  unbound:
    image: mvance/unbound:latest
    container_name: unbound
    restart: unless-stopped
    networks:
      pihole_net:
        ipv4_address: 10.2.0.2
    volumes:
      - /home/homelab/pihole-unbound-data/unbound/etc-unbound:/etc/unbound

  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    hostname: pihole
    domainname: lan
    restart: unless-stopped
    environment:
      TZ: "America/Sao_Paulo"
      WEBPASSWORD: ""
    volumes:
      - /home/homelab/pihole-unbound-data/pihole/etc-pihole:/etc/pihole
      - /home/homelab/pihole-unbound-data/pihole/etc-dnsmasq.d:/etc/dnsmasq.d
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "67:67/udp"
      - "80:80"
      - "443:443"
    networks:
      pihole_net:
        ipv4_address: 10.2.0.3
    cap_add:
      - NET_ADMIN

networks:
  pihole_net:
    driver: bridge
    ipam:
      config:
        - subnet: 10.2.0.0/29
```

### 4. Subir os Containers

Para iniciar os containers:

```bash
docker compose up -d
```

### 5. Configurar a Senha do Pi-hole

Execute o comando abaixo para configurar a senha de administração do Pi-hole:

```bash
docker exec -it pihole pihole setpassword
```

### 6. Verificar o Status dos Containers

Use os seguintes comandos para verificar o status dos containers, redes, volumes e imagens:

```bash
docker ps -a
docker images
docker network ls
docker network inspect pihole-unbound_pihole_net
docker volume ls
docker compose ls
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' pihole
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' unbound
```

### 7. Testar a Resolução DNS

Para testar a resolução DNS:

```bash
dig +trace google.com
dig +dnssec +multi dnssec-failed.org @10.2.0.2
```

### 8. Verificar o Status com Formato de Tabela

Para verificar o status dos containers de forma mais legível:

```bash
docker ps --format "table {{.Names}}	{{.Status}}	{{.Ports}}"
```

---

**Nota**: Essa configuração assume que você está utilizando um único servidor para executar o Pi-hole e o Unbound. Certifique-se de ajustar os arquivos conforme necessário para atender a configurações específicas do seu homelab.
