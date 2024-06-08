## How to install

```bash
cd /home/homelab/
git clone https://github.com/oobabooga/text-generation-webui.git
cd /home/homelab/text-generation-webui/
./start_linux.sh #sair ctrl+c

nano CMD_FLAGS.txt #aleter a linha # --listen --api para  --share

./cmd_linux.sh

pip install --upgrade TTS

./start_linux.sh #ativar tts e aplicar e apos carregar sair ctrl+c

exit

cd /home/homelab/text-generation-webui/extensions/

git clone https://github.com/erew123/alltalk_tts.git

cd /home/homelab/text-generation-webui/

./cmd_linux.sh

cd /home/homelab/text-generation-webui/extensions/alltalk_tts/

chmod +x atsetup.sh
./atsetup.sh

cd /home/homelab/text-generation-webui/
./start_linux.sh # ativar alltalk , aplicar default e sair ctrl+c

./update_wizard_linux.sh

sudo apt-get install portaudio19-dev

./start_linux.sh # de cima para baixo ativar todos até openai, menos multimodal e ngrok apos carregar tire todos e selecione abaixo de openai menos o superboogav2.

./cmd_linux.sh

pip install --upgrade TTS

```
