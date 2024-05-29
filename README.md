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
apt update
apt install -y git build-essential libssl-dev
git clone https://github.com/oobabooga/text-generation-webui.git
cd text-generation-webui/
./start_linux.sh #apos a instalação e criação ctrl+c para sair

./update_wizard_linux.sh #atualizar webui e extenções

exit

nano CMD_FLAGS.txt #tirar o # da linha --listen --api apos ctrl+X salvar e sair
./start_linux.sh #na aba extensions ativar o coqui tts e aplicar do default

```

