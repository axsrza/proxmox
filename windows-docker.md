## Instalação windows

```bash
cd /
cd
mkdir windows
cd windows
nano docker-compose.yml

```

## docker-compose.yml

```bash
version: "3"
services:
  windows:
    image: dockurr/windows
    container_name: windows
    devices:
      - /dev/kvm
    cap_add:
      - NET_ADMIN
    ports:
      - "8006:8006"
      - "3390:3389/tcp"
      - "3390:3389/udp"
    stop_grace_period: 2m
    environment:
      VERSION: "win10"
      RAM_SIZE: "4G"
      CPU_CORES: "4"
      DISK_SIZE: "30G"
    volumes:
      - ./data:/storage

```

## Executar docker

```bash
sudo docker compose up -d

```
