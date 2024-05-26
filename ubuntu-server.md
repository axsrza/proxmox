## Após Instalação

```bash
sudo passwd root

nano /etc/ssh/sshd_config
PermitRootLogin yes

sudo reboot

```

## Nvidia Driver

```bash
sudo apt update && sudo apt upgrade
ubuntu-drivers devices

sudo apt install nvidia-driver-X

sudo reboot

```
