# Atualizar pacotes e instalar dependências do sistema
apt update
apt install -y git build-essential libssl-dev

# Instalar Ollama
curl -fsSL https://ollama.com/install.sh | sh

# Instalar Node.js
curl -fsSL https://deb.nodesource.com/setup_current.x | sudo -E bash -
apt-get install -y nodejs

# Instalar Python
apt update
apt install -y python3 python3-venv python3-dev

# Clonar o repositório
git clone https://github.com/open-webui/open-webui.git
cd open-webui/

# Copiar o arquivo .env.example para .env
cp -RPp .env.example .env

# Construir o frontend
npm install
npm run build

# Configurar e executar o backend
cd ./backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt -U
bash start.sh
