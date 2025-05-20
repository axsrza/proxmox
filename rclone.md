# 🚀 Rclone + Firefox via Docker no Alpine Linux

Este guia explica como instalar o **rclone**, configurar um **remote**, e executar o navegador **Firefox** via Docker no Alpine Linux. Inclui duas opções de container Docker.

---

## ⚡ Requisitos

Instale pacotes necessários:

```sh
apk add unzip
```

Instale o `rclone` diretamente do script oficial:

```sh
curl https://rclone.org/install.sh | bash
```

---

## ⚙️ Configurar o Rclone

Inicie a configuração interativa:

```sh
rclone config
```

Siga o assistente para configurar um remote (por exemplo, Google Drive).

---

## 🚀 Rodar Firefox via Docker

Recomenda-se uma das duas opções abaixo para executar o Firefox em container.

### Opção 1: jlesage/firefox

```sh
docker run -d \
  --network host \
  --name firefox \
  jlesage/firefox
```

### Opção 2: linuxserver/firefox

```sh
docker run -d \
  --name firefox \
  --network host \
  linuxserver/firefox
```

> Ambas exigem o acesso via web ou VNC, conforme o container escolhido.

---

## 📂 Sincronizar arquivos com Rclone

Exemplo de cópia de pasta local para o Google Drive:

```sh
rclone copy /home/music gdrive:/home/music --progress
```

> Substitua `gdrive:` pelo nome do seu remote configurado.

---

## ✅ Finalizando

O `rclone` está pronto para uso e o navegador pode ser executado em ambiente isolado via Docker. Ideal para ambientes leves como Alpine Linux e uso em homelabs.

---

## 👉 Repositórios Relevantes

* [https://rclone.org](https://rclone.org)
* [https://hub.docker.com/r/jlesage/firefox](https://hub.docker.com/r/jlesage/firefox)
* [https://hub.docker.com/r/linuxserver/firefox](https://hub.docker.com/r/linuxserver/firefox)

Para sugestões, abra uma issue ou contribua com um PR. 🚀
