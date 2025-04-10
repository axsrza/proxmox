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

#### 📅 Instalar `cloudflared`

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

## 🗼 Configurar IP fixo com `systemd-networkd` de forma segura

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
nano /etc/resolv.conf

nameserver 192.168.1.1

systemctl enable --now systemd-networkd
systemctl restart systemd-networkd
sudo reboot
```

6. **Desabilitar o serviço de rede antigo com segurança:**

```bash
nano /etc/resolv.conf

nameserver 192.168.1.1

systemctl restart systemd-networkd
systemctl disable --now networking
sudo reboot
```

#### 🚀 att tudo

```bash
sudo apt update && sudo apt full-upgrade -y && sudo apt autoremove -y && sudo apt autoclean
```

VALIDAR:

## Remoção e Desativação de Serviços no Linux

Este guia detalha a função de cada serviço ou pacote desabilitado/remoção no sistema operacional Debian/Ubuntu para uso em homelabs otimizados e minimalistas.

---

### 1. Desabilitar Serviços via `systemctl`

```bash
sudo systemctl disable \
  apt-daily.service apt-daily-upgrade.service \
  logrotate.service man-db.service dpkg-db-backup.service \
  fstrim.service bluetooth.service networking.service \
  anacron.service e2scrub_all.service e2scrub_reap.service
```

**Funções detalhadas:**
- **apt-daily.service / apt-daily-upgrade.service**: Agendam atualizações automáticas de pacotes pelo APT. Em homelabs, onde o controle manual é preferível, isso é desnecessário.
- **logrotate.service**: Roda o `logrotate`, que gerencia rotação e compressão de logs. Pode ser substituído por scripts personalizados se necessário.
- **man-db.service**: Atualização das páginas de manual. Pode ser desabilitado se não forem utilizadas man pages localmente.
- **dpkg-db-backup.service**: Backup automático da base de dados de pacotes dpkg. Em ambientes controlados, isso pode ser omitido.
- **fstrim.service**: Envia comandos TRIM para SSDs, liberando blocos inativos. Pode ser feito manualmente quando necessário.
- **bluetooth.service**: Gerencia conexões Bluetooth. Desnecessário em servidores sem interface sem fio.
- **networking.service**: Sistema tradicional de gerenciamento de redes (usado com ifupdown). Substituído por `systemd-networkd` ou `netplan`.
- **anacron.service**: Executa tarefas perdidas do `cron` se o sistema estiver desligado. Desnecessário em servidores que ficam ligados.
- **e2scrub_all / e2scrub_reap**: Verifica e "resgata" sistemas de arquivos ext4 em background. Redundante em ambientes bem monitorados.

---

### 2. Remoção de Pacotes com `apt`

#### 2.1 Comando:
```bash
sudo apt remove --purge anacron bluetooth ifupdown -y
```

- **anacron**: Ver item anterior.
- **bluetooth**: Remove suporte completo a dispositivos Bluetooth.
- **ifupdown**: Ferramenta antiga para gerenciamento de interfaces de rede. Substituído pelo `systemd-networkd`.

#### 2.2 Comando:
```bash
sudo apt purge \
  avahi-autoipd discover discover-data \
  inetutils-telnet emacsen-common \
  iamerican ibritish ienglish-common ispell dictionaries-common \
  debian-faq doc-debian installation-report iw -y
```

**Função de cada pacote:**
- **avahi-autoipd**: Atribui IP automático (zeroconf). Pode interferir com IPs estáticos.
- **discover / discover-data**: Detecta hardware automaticamente no boot. Redundante para servidores fixos.
- **inetutils-telnet**: Cliente telnet, protocolo obsoleto e inseguro.
- **emacsen-common**: Dependência de instalação de editores Emacs. Supérfluo.
- **iamerican / ibritish / ienglish-common / ispell / dictionaries-common**: Dicionários e corretores ortográficos.
- **debian-faq / doc-debian**: Documentação oficial, removida para economizar espaço.
- **installation-report**: Ferramentas de diagnóstico do instalador Debian.
- **iw**: Ferramentas de configuração de dispositivos Wi-Fi, desnecessárias sem hardware compatível.

#### 2.3 Comando:
```bash
sudo apt autoremove --purge -y
```

Remove dependências não utilizadas deixadas por outros pacotes.

---

### 3. Limpeza de Serviços Systemd

#### 3.1 Reset de falhas pendentes:
```bash
sudo systemctl reset-failed
```

Evita que falhas passadas interfiram nos logs ou status atual.

#### 3.2 Remoção de arquivos systemd supérfluos:
```bash
sudo find /etc/systemd/system /lib/systemd/system \
  -name '*auditd*' -o -name '*connman*' -o -name '*console-screen*' \
  -o -name '*display-manager*' -o -name '*firewalld*' \
  -o -name '*NetworkManager*' -o -name '*plymouth*' \
  -o -name '*syslog*' -o -name '*hwdb*' -o -name '*oomd*' \
  -o -name '*update-done*' -o -name '*vconsole*' -delete
```

**Funções comuns dos arquivos removidos:**
- **auditd**: Demonstração de auditoria do sistema.
- **connman**: Gerenciador de conexão (Wi-Fi/Ethernet).
- **console-screen / display-manager / plymouth**: Interfaces gráficas e bootsplash.
- **firewalld / NetworkManager**: Sistemas mais pesados para rede e firewall, trocados por ferramentas mais leves.
- **syslog**: Logs do sistema, substituídos pelo `journald`.
- **hwdb / oomd / update-done / vconsole**: Configurações de hardware, memória, teclado e status do update.

---

### 4. Mascaramento de serviços

```bash
sudo systemctl disable \
  wpa_supplicant.service wpa_supplicant@.service \
  wpa_supplicant-nl80211@.service wpa_supplicant-wired@.service

sudo systemctl mask \
  wpa_supplicant.service wpa_supplicant@.service \
  wpa_supplicant-nl80211@.service wpa_supplicant-wired@.service

sudo systemctl mask e2scrub_all.service e2scrub_reap.service
```

**Explicação:**
- **wpa_supplicant**: Responsável por conexões Wi-Fi. Mascarado para impedir sua ativação.
- **e2scrub**: Verificação de integridade de sistemas de arquivos ext4. Pode ser desnecessário se já houver backups e manutenção preventiva.

---

### 5. Recarregar `systemd` e limpar logs

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo journalctl --rotate
sudo journalctl --vacuum-time=1s
```

**Função:**
- **daemon-reexec / reload**: Atualiza o `systemd` após modificações.
- **journalctl rotate/vacuum**: Força a rotação e limpeza de logs antigos.

---

### 6. Reiniciar o sistema

```bash
sudo reboot
```

Aplica as mudanças feitas no sistema de forma limpa e segura.

