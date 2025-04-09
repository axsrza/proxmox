# Homelab

### 1. Preparacão do Sistema
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

Crie os diretórios diretamente no home do usuário `homelab`:

```bash
mkdir -p ~homelab/pihole-unbound
mkdir -p ~homelab/pihole-unbound-data/pihole/etc-pihole
mkdir -p ~homelab/pihole-unbound-data/pihole/etc-dnsmasq.d
mkdir -p ~homelab/pihole-unbound-data/unbound/etc-unbound
cd ~homelab/pihole-unbound
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

### 6. Testes

Use os seguintes comandos para verificar o status dos containers, redes, volumes e imagens:

```bash
docker ps -a
docker images
docker network ls
docker volume ls
docker compose ls
docker network inspect pihole-unbound_pihole_net
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' pihole
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' unbound
dig @10.2.0.3 google.com
dig @10.2.0.2 google.com
dig +dnssec +multi dnssec-failed.org @10.2.0.2
dig +trace google.com
dig +dnssec +multi dnssec-failed.org @10.2.0.2
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```
