## Após a instalação do Ubuntu Server

```bash
#ainda no terminal, logar na conta criada na instalação
sudo passwd root #digite a senha criada na instalação
#defina uma senha para o root

sudo reboot

```

## Via SSH logar na conta criada

```bash
#logar na conta criada na instalação
su #digitar a senha para o root

cd /
cd
nano /etc/ssh/sshd_config #retire o # na frente da linha que consta PermitRootLogin prohibit-password e substitua por PermitRootLogin yes

sudo reboot

```

## Instalação docker-engine

```bash
#https://docs.docker.com/engine/install/ubuntu/
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
docker --version

sudo docker run hello-world

```

## Instalação docker-compose

```bash
#https://docs.docker.com/compose/install/standalone/
curl -SL https://github.com/docker/compose/releases/download/v2.27.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker compose version

```

## Nvidia Driver

```bash
sudo apt update && sudo apt upgrade
ubuntu-drivers devices

sudo apt install nvidia-driver-X #substitua o X pelo numero da distro que consta recommended

sudo reboot

```

## Nvidia Container Toolkit

```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit

sudo reboot

```

## Monitorar GPU

```bash
watch -n 1 nvidia-smi

```

## Open WebUI

```bash
#https://github.com/open-webui/open-webui
#Installing Open WebUI with Bundled Ollama Support
#With GPU Support: Utilize GPU resources by running the following command
docker run -d -p 3000:8080 --gpus=all -v ollama:/root/.ollama -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:ollama

docker ps

```

## Adicionando Modelos

```bash
docker exec -it open-webui bash

ollama pull llama3 #subistitua llama3 pelo models desejado https://ollama.com/library

exit

```

## Oobabooga text-generation-webui

```bash
cd /
cd
apt update
apt install -y git build-essential libssl-dev
git clone https://github.com/oobabooga/text-generation-webui.git
cd text-generation-webui/
./start_linux.sh
#apos a instalação e criação ctrl+c para sair

./update_wizard_linux.sh #atualizar B) Install/update extensions requirements e A) Update the web UI

nano CMD_FLAGS.txt #tirar o # da linha --listen --api apos ctrl+X salvar e sair

./start_linux.sh #na aba extensions ativar o coqui tts e aplicar do default

```

## Intalação filebrowser

```bash
#https://technotim.live/posts/meet-file-browser/
cd /
cd
mkdir filebrowser
cd filebrowser
touch docker-compose.yml
nano docker-compose.yml # copy the contents from below docker-compose.yml

```

## docker-compose.yml

```bash
---
version: '3'
services:
  file-browser:
    image: filebrowser/filebrowser
    container_name: file-browser
    user: 0:0
    ports:
      - 8081:80
    volumes:
      - /root:/srv
      - /root/filebrowser/filebrowser.db:/database.db
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true

```

## Iniciar filebrowser

```bash
touch filebrowser.db
docker compose up -d --force-recreate

```

## Expandir 100% do ssd

```bash
lsblk
lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
resize2fs /dev/ubuntu-vg/ubuntu-lv
sudo reboot

```

## Instalação pihole

```bash
curl -sSL https://install.pi-hole.net | bash

```

## Instalação unbound

```bash
sudo apt install unbound
wget https://www.internic.net/domain/named.root -qO- | sudo tee /var/lib/unbound/root.hints
nano /etc/unbound/unbound.conf.d/pi-hole.conf

```

## pi-hole.conf

```bash
server:
    # If no logfile is specified, syslog is used
    # logfile: "/var/log/unbound/unbound.log"
    verbosity: 0

    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes

    # May be set to yes if you have IPv6 connectivity
    do-ip6: no

    # You want to leave this to no unless you have *native* IPv6. With 6to4 and
    # Terredo tunnels your web browser should favor IPv4 for the same reasons
    prefer-ip6: no

    # Use this only when you downloaded the list of primary root servers!
    # If you use the default dns-root-data package, unbound will find it automatically
    #root-hints: "/var/lib/unbound/root.hints"

    # Trust glue only if it is within the server's authority
    harden-glue: yes

    # Require DNSSEC data for trust-anchored zones, if such data is absent, the zone becomes BOGUS
    harden-dnssec-stripped: yes

    # Don't use Capitalization randomization as it known to cause DNSSEC issues sometimes
    # see https://discourse.pi-hole.net/t/unbound-stubby-or-dnscrypt-proxy/9378 for further details
    use-caps-for-id: no

    # Reduce EDNS reassembly buffer size.
    # IP fragmentation is unreliable on the Internet today, and can cause
    # transmission failures when large DNS messages are sent via UDP. Even
    # when fragmentation does work, it may not be secure; it is theoretically
    # possible to spoof parts of a fragmented DNS message, without easy
    # detection at the receiving end. Recently, there was an excellent study
    # >>> Defragmenting DNS - Determining the optimal maximum UDP response size for DNS <<<
    # by Axel Koolhaas, and Tjeerd Slokker (https://indico.dns-oarc.net/event/36/contributions/776/)
    # in collaboration with NLnet Labs explored DNS using real world data from the
    # the RIPE Atlas probes and the researchers suggested different values for
    # IPv4 and IPv6 and in different scenarios. They advise that servers should
    # be configured to limit DNS messages sent over UDP to a size that will not
    # trigger fragmentation on typical network links. DNS servers can switch
    # from UDP to TCP when a DNS response is too big to fit in this limited
    # buffer size. This value has also been suggested in DNS Flag Day 2020.
    edns-buffer-size: 1232

    # Perform prefetching of close to expired message cache entries
    # This only applies to domains that have been frequently queried
    prefetch: yes

    # One thread should be sufficient, can be increased on beefy machines. In reality for most users running on small networks or on a single machine, it should be unnecessary to seek performance enhancement by increasing num-threads above 1.
    num-threads: 1

    # Ensure kernel buffer is large enough to not lose messages in traffic spikes
    so-rcvbuf: 1m

    # Ensure privacy of local IP ranges
    private-address: 192.168.0.0/16
    private-address: 169.254.0.0/16
    private-address: 172.16.0.0/12
    private-address: 10.0.0.0/8
    private-address: fd00::/8
    private-address: fe80::/10

```

## Teste unbound

```bash
sudo service unbound restart
dig pi-hole.net @127.0.0.1 -p 5335

```

## Instalação adguard

```bash
cd /
cd
mkdir -p /root/adguard/work
mkdir -p /root/adguard/conf
cd /root/adguard

docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=enp5s0 mynet

docker run --name adguardhome \
    --restart unless-stopped \
    --network mynet \
    --ip 192.168.1.112 \
    -v $(pwd)/work:/opt/adguardhome/work \
    -v $(pwd)/conf:/opt/adguardhome/conf \
    -p 8080:80/tcp -p 443:443/tcp -p 3001:3000/tcp \
    -d adguard/adguardhome

```

## Instalação prometheus node-exporter grafana

```bash
cd /
cd
mkdir -p /root/monitoring
cd /root/monitoring
nano docker-compose.yml

```

## docker-compose.yml

```bash
version: '3.7'

services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - ./prometheus:/prometheus
    ports:
      - "9091:9090"  # Porta 9091 para evitar conflito
    networks:
      - monitoring_net
    restart: unless-stopped
    user: "0:0"  # Define o usuário e grupo como root

  node-exporter:
    image: prom/node-exporter
    container_name: node-exporter
    ports:
      - "9101:9100"  # Porta 9101 para evitar conflito
    networks:
      - monitoring_net
    restart: unless-stopped
    user: "0:0"  # Define o usuário e grupo como root
  
  grafana:
    image: grafana/grafana
    container_name: grafana
    volumes:
      - ./grafana:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    ports:
      - "3001:3000"  # Porta 3001 para evitar conflito
    networks:
      - monitoring_net
    restart: unless-stopped
    user: "0:0"  # Define o usuário e grupo como root

networks:
  monitoring_net:
    driver: bridge

```

## Instalação prometheus

```bash
nano prometheus.yml

```

## prometheus.yml

```bash
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['prometheus:9090']  # Alterado para o nome do serviço

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['node-exporter:9100']  # Alterado para o nome do serviço

```

## inicie os serviços com docker compose

```bash
sudo docker compose up -d

```
