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
