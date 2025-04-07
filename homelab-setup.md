# 🏠 Homelab com Debian 12 Minimal

## 🔧 Instalação e Preparação Inicial

### 1. Acessar o terminal local e verificar IP/interface:
```bash
ip -c a
```

### 2. Conectar via SSH (PuTTY) e definir senha root:
```bash
sudo passwd root
```

### 3. Logar como root:
```bash
su
```

### 4. Instalar pacotes essenciais:
```bash
sudo apt install unbound curl btop ufw lighttpd cloudflared -y
```

---

## 🌐 Unbound (Resolver DNS Local)

### 1. Baixar root hints:
```bash
sudo wget https://www.internic.net/domain/named.root -qO- | sudo tee /var/lib/unbound/root.hints
```

### 2. Criar arquivo de configuração:
```bash
sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf
```

#### Conteúdo:
```bash
server:
    verbosity: 0
    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes
    do-ip6: yes
    prefer-ip6: no
    edns-buffer-size: 1232
    prefetch: yes
    num-threads: 1
    so-rcvbuf: 1m
    private-address: 192.168.0.0/16
    private-address: 169.254.0.0/16
    private-address: 172.16.0.0/12
    private-address: 10.0.0.0/8
    private-address: fd00::/8
    private-address: fe80::/10
```

### 3. Reiniciar e testar:
```bash
sudo service unbound restart
sudo dig pi-hole.net @127.0.0.1 -p 5335
```

---

## 🌍 IP Fixo

### Editar interfaces:
```bash
sudo nano /etc/network/interfaces
```

#### Adicionar abaixo de `# The primary network interface:`
```bash
allow-hotplug enx00e04c680094
iface enx00e04c680094 inet static
    address 192.168.1.99
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 127.0.0.1
```

### Atualizar fuso horário e rede:
```bash
sudo timedatectl set-timezone America/Sao_Paulo
sudo systemctl restart networking
```

---

## 🧱 Instalação do Pi-hole

### Instalar:
```bash
curl -sSL https://install.pi-hole.net | bash
```

> Durante a instalação, selecione `127.0.0.1#5335` como servidor DNS.

### Definir senha de acesso:
```bash
sudo pihole setpassword
```

### Fixar DNS local:
```bash
sudo nano /etc/resolv.conf
```
```bash
nameserver 127.0.0.1
```

### Impedir alterações:
```bash
sudo chattr +i /etc/resolv.conf
```

---

## 🔥 Firewall (UFW)
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow 53/tcp
sudo ufw allow 53/udp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 4711/tcp
sudo ufw allow 67/udp
sudo ufw allow 67/tcp
sudo ufw allow 123/udp
sudo ufw allow 546:547/udp
sudo ufw enable
```

---

## 🛡️ Tailscale

### Instalar:
```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

### Ativar roteamento:
```bash
sudo echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo echo "net.ipv6.conf.all.forwarding = 1" | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

### Subir com roteamento:
```bash
sudo tailscale up --accept-dns=false --advertise-routes=192.168.1.0/24 --advertise-exit-node
```

---

## ✅ Verificações e Limpeza
```bash
sudo systemctl status unbound tailscaled pihole-FTL
sudo apt update && sudo apt full-upgrade -y
sudo apt autoremove -y && sudo apt autoclean
```

---

## 📋 Reservas DHCP (Exemplo)
```
84:b4:d2:32:f3:3a,aspirador
5c:c9:d3:70:a5:3b,acer
...
```

---

## 🌐 Blog (Lighttpd + Pi-hole)

### Criar diretório e arquivo index:
```bash
sudo mkdir -p /var/www/html/blog
sudo echo "<h1>Hello World</h1>" | sudo tee /var/www/html/blog/index.html
```

### Editar o `lighttpd.conf`:
```bash
sudo nano /etc/lighttpd/lighttpd.conf
```
```bash
server.document-root = "/var/www/html/blog"
server.dir-listing = "disable"
```

### Criar alias para o Pi-hole:
```bash
sudo nano /etc/lighttpd/conf-available/99-pihole-admin-alias.conf
```
```bash
alias.url += (
  "/admin" => "/var/www/html/admin",
  "/admin/" => "/var/www/html/admin/"
)
```

### Ativar o alias e reiniciar:
```bash
sudo lighty-enable-mod 99-pihole-admin-alias
sudo systemctl restart lighttpd
```

---

## ☁️ Cloudflare Tunnel

### Autenticar e criar túnel:
```bash
sudo cloudflared tunnel login
sudo cloudflared tunnel create blog-tunnel
```

### Criar config:
```bash
sudo mkdir -p /etc/cloudflared
sudo nano /etc/cloudflared/config.yml
```
```yaml
tunnel: blog-tunnel
credentials-file: /root/.cloudflared/blog-tunnel.json

ingress:
  - hostname: azzor1337x.shop
    service: http://localhost
  - service: http_status:404
```

### Mapear DNS no Cloudflare:
```bash
cloudflared tunnel route dns blog-tunnel azzor1337x.shop
```

### Iniciar e habilitar serviço:
```bash
sudo cloudflared service install
sudo systemctl status cloudflared
```

---

✨ Pronto! Seu homelab com blog e Pi-hole acessível via domínio está configurado!

