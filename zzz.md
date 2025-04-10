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

#### 📁 Estrutura do diretório:

```bash
mkdir -p ~homelab/blog/html
```

#### 📜 Criar um index.html simples

```bash
nano ~homelab/blog/html/index.html
```

##### Conteúdo do `index.html`

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

#### 📜 Criar um `docker-compose.yml`

```bash
nano ~homelab/blog/docker-compose.yml
```

##### Conteúdo do `docker-compose.yml`

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

#### 🚀 Subir o container

```bash
cd ~homelab/blog
chmod +x /usr/local/bin/docker-compose
docker-compose up -d
```

#### 📥 Instalar `cloudflared`

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

#### 🛠️ Criar o túnel

```bash
cloudflared tunnel delete blog
cloudflared tunnel create blog  # ID será gerado automaticamente
```

#### 📁 Criar o arquivo de configuração

```bash
nano /root/.cloudflared/config.yml
```

##### Exemplo de `config.yml`

```yaml
tunnel: blog
credentials-file: /root/.cloudflared/INSERIR_ID_DO_TUNNEL.json

ingress:
  - hostname: azzor1337x.shop
    service: http://127.0.0.1:8888
  - service: http_status:404
```

####  Rodar o túnel manualmente

```bash
cloudflared tunnel run blog
```

```bash
cloudflared service install
```

1. **Identificar a interface:**
```bash
ip -c a
```

2. **Criar arquivo de configuração de rede:**
```bash
nano /etc/systemd/network/10-wired.network
```

Exemplo:
```ini
[Match]
Name=enx00e04c680094

[Network]
Address=192.168.1.99/24
Gateway=192.168.1.1
DNS=192.168.1.1
```

3. **Habilitar serviços:**
```bash
systemctl disable --now networking
systemctl enable --now systemd-networkd
systemctl enable --now systemd-resolved
ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
```

4. **Reiniciar rede ou o sistema:**
```bash
reboot
# ou
systemctl restart systemd-networkd
```

5. **Reconectar no novo IP via PuTTY**

---

## ✅ Dica de Segurança

Antes de desabilitar o `networking.service`, valide a configuração com:
```bash
systemctl start systemd-networkd
networkctl status
```
Se tudo certo:
```bash
systemctl disable --now networking
```

---

## 🧠 Referências Rápidas
- Arquivo de rede: `/etc/systemd/network/*.network`
- Logs: `journalctl -u systemd-networkd`
- Status da rede: `networkctl`

#### 🚀 att tudo

```bash
sudo apt update && sudo apt full-upgrade -y && sudo apt autoremove -y && sudo apt autoclean
```








## 🛜 Configurar IP fixo com `systemd-networkd` de forma segura

1. **Identificar a interface atual:**

```bash
ip -c a
```

2. **Criar o arquivo de configuração com o mesmo IP da rede atual:**

```bash
nano /etc/systemd/network/10-wired.network
```

Exemplo:

```ini
[Match]
Name=enx00e04c680094

[Network]
Address=192.168.1.99/24
Gateway=192.168.1.1
DNS=192.168.1.1
```

3. **Ativar os novos serviços de rede, mantendo o atual por enquanto:**

```bash
systemctl enable --now systemd-networkd
systemctl enable --now systemd-resolved
ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
```

4. **Testar a conectividade:**

```bash
systemctl restart systemd-networkd
systemctl restart systemd-resolved
```

5. **Reconectar via PuTTY no mesmo IP (agora fixo)**

6. **Desabilitar o serviço de rede antigo com segurança:**

```bash
systemctl disable --now networking
```

---


#### 🚀 att tudo

```bash
sudo apt update && sudo apt full-upgrade -y && sudo apt autoremove -y && sudo apt autoclean
```

