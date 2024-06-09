## Open WebUI

```bash
#https://github.com/open-webui/open-webui
#Installing Open WebUI with Bundled Ollama Support
#With GPU Support: Utilize GPU resources by running the following command
docker run -d -p 3000:8080 --gpus=all -v ollama:/root/.ollama -v open-webui:/app/backend/data --name open-webui --restart unless-stopped ghcr.io/open-webui/open-webui:ollama

docker ps

```

## Stop / Restart

```bash
docker stop open-webui

docker restart open-webui

```


## Adicionando Modelos

```bash
docker exec -it open-webui bash

ollama pull llama3 #subistitua llama3 pelo models desejado https://ollama.com/library

exit

```
