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

## Docker

```bash
sudo apt install docker.io

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

## Open WebUI

```bash
docker run -d -p 3000:8080 --gpus=all -v ollama:/root/.ollama -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:ollama

```

## Adicioando Modelos

```bash
docker exec -it open-webui bash
ollama pull llama3

exit

```

## Monitorar GPU

```bash
watch -n 1 nvidia-smi

```
