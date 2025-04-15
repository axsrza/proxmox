fico assim então?

# 🛠️ Homelab Setup - Debian 12 Minimal com LVM, Docker, Unbound e Pi-hole

## 📦 Pré-Instalação: Limpando o Disco

Antes de instalar o Debian 12, é recomendável apagar completamente o disco para evitar conflitos com partições antigas.

1. Assim que abrir o instalador, pressione `Ctrl + Alt + F2` para abrir um terminal.
2. Execute:
   ```bash
   dd if=/dev/zero of=/dev/sda bs=1M count=10
   ```
3. Reinicie com `Ctrl + Alt + Del`.

---

## 📍 Particionamento Manual com LVM

### Etapas no Instalador

1. Em "Partition disks":
   ```
   Manual
   ```
2. Escolha o disco:
   ```
   [ ] SCSI1 (0,0,0) (sda) - 120.0 GB ATA SSD
   ```
3. Confirme:
   ```
   Yes – Create a new empty partition table on this device
   ```
4. Crie a partição para LVM:
   ```
   > FREE SPACE (120.0 GB)
     > Create a new partition
   ```
   - Tamanho: `110 GB`
   - Tipo: `Primary`
   - Localização: `Beginning`
   - Use as: `physical volume for LVM`
   - Finalize: `Done setting up the partition`

5. Configure o LVM:
   ```
   Configure the Logical Volume Manager
   ```
6. Confirme:
   ```
   Write the changes to the disk and configure LVM? → Yes
   ```
7. Crie o volume group:
   ```
   Create volume group → homelab-vg
   → selecione /dev/sda1
   ```
8. Crie os volumes lógicos:
   - `root` → 10 GB
   - `home` → 10 GB
   - `var` → 10 GB
   - `opt` → 10 GB
   - `swap` → 2 GB

Etapa 9 - Configuração do Sistema de Arquivos:
/ → ext4 → /dev/mapper/homelab--vg-root

/home → ext4 → /dev/mapper/homelab--vg-home

/var → ext4 → /dev/mapper/homelab--vg-var

/opt → ext4 → /dev/mapper/homelab--vg-opt

swap → swap → /dev/mapper/homelab--vg-swap

---

## 🚀 Primeiro Boot e IP Fixo (systemd-networkd)

```bash
ip -c a
```

```bash
sudo passwd root
su
```

```bash
sudo timedatectl set-timezone America/Sao_Paulo
```

### Udev: Nome fixo para interface de rede

```bash
sudo nano /etc/udev/rules.d/10-network.rules
```

```bash
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:e0:4c:68:00:94", NAME="eth0"
```

### systemd-networkd: IP fixo

```bash
sudo nano /etc/systemd/network/10-wired.network
```

```ini
[Match]
Name=eth0

[Network]
Address=192.168.1.99/24
Gateway=192.168.1.1
DNS=192.168.1.1
#DNS=127.0.0.1
```

```bash
sudo rm /etc/resolv.conf
sudo nano /etc/resolv.conf
```

```bash
nameserver 192.168.1.1
#nameserver 127.0.0.1
```

```bash
sudo systemctl enable --now systemd-networkd
sudo systemctl disable --now networking
sudo reboot
```

---

## 🐳 Instalar Docker + Compose

```bash
sudo apt install iptables btop -y
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove -y $pkg; done
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
chmod a+r /etc/apt/keyrings/docker.asc
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
  https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
sudo curl -SL https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -sf /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose version
```

---

## ⚙️ Unbound como DNS Recursivo

```bash
sudo apt install unbound -y
sudo wget https://www.internic.net/domain/named.root -qO- | sudo tee /var/lib/unbound/root.hints
sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf
```

```conf
server:
    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes
    do-ip6: yes
    prefer-ip6: no
    harden-glue: yes
    harden-dnssec-stripped: yes
    use-caps-for-id: no
    prefetch: yes
    num-threads: 1
    so-rcvbuf: 1m
    edns-buffer-size: 1232
    private-address: 192.168.0.0/16
    private-address: 169.254.0.0/16
    private-address: 172.16.0.0/12
    private-address: 10.0.0.0/8
    private-address: fd00::/8
    private-address: fe80::/10
    private-address: 192.0.2.0/24
    private-address: 198.51.100.0/24
    private-address: 203.0.113.0/24
    private-address: 255.255.255.255/32
    private-address: 2001:db8::/32
```

```bash
sudo systemctl restart unbound
dig pi-hole.net @127.0.0.1 -p 5335
```

---

## ❌ Pi-hole com Docker

```bash
mkdir -p /opt/pihole
nano /opt/pihole/docker-compose.yml
```

```yaml
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    network_mode: host
    environment:
      TZ: 'America/Sao_Paulo'
      WEBPASSWORD: ''
    volumes:
      - './etc-pihole:/etc/pihole'
      - './etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
```

```bash
cd /opt/pihole
docker-compose up -d
```

```bash
docker exec -it pihole pihole setpassword
```

Ajuste o DNS via Web UI:
```
127.0.0.1#5335
```

---

## 📜 Ajustar DNS do Host

```bash
sudo nano /etc/systemd/network/10-wired.network
```

```bash
sudo nano /etc/resolv.conf
```

```bash
sudo reboot
```

---

## 🧭 Portainer via Docker

```bash
mkdir -p /opt/portainer
nano /opt/portainer/docker-compose.yml
```

```yaml
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data

volumes:
  portainer_data:
```

```bash
cd /opt/portainer
docker-compose up -d
```

---

## ✅ Testes Finais

```bash
ip -c a
hostname -I
docker ps -a
docker images
docker network ls
docker volume ls
docker compose ls
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

