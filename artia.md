
## 

```
/home/ticket/
├── Dockerfile
├── backend
│   ├── index.js
│   └── package.json
├── docker-compose.yml
└── site
    └── index.html
```

## /home/ticket/docker-compose.yml

```
version: "3.9"
services:
  ticket:
    build: .
    container_name: ticket
    ports:
      - "8002:80"
    restart: unless-stopped
```

## /home/ticket/Dockerfile

```
FROM node:18-alpine

# Diretório de trabalho dentro do container
WORKDIR /app

# Instalar dependências
COPY backend/package.json .
RUN npm install

# Copiar backend
COPY backend/index.js ./index.js

# Copiar site estático
COPY site /usr/share/nginx/html

# Expor porta HTTP
EXPOSE 80

# Rodar API
CMD ["node", "index.js"]
```

## /home/ticket/backend/package.json

```
{
  "name": "ticket-backend",
  "version": "1.0.0",
  "main": "index.js",
  "type": "module",
  "dependencies": {
    "express": "^4.19.2",
    "node-fetch": "^3.3.2"
  }
}
```

##  /home/ticket/backend/index.js

```
import express from "express";
import fetch from "node-fetch";

const app = express();
app.use(express.json());
app.use(express.static("/usr/share/nginx/html"));

// Configurações fixas
const API_URL = "https://app.artia.com/graphql";
const CLIENT_ID = "vmAVHdx1N2D2LdC-x3OehM9pMz70E-YidJXVsQVqckY";
const SECRET = "PcvunYNwjwi22h5sWrqBIGcEl7vWy_B-noKssKddrSc";
const ORG_ID = "141029";
const ACCOUNT_ID = 5096175;
const FOLDER_ID = 5975480;
const RESPONSIBLE_ID = 258091;
const FOLDER_TYPE_ID = 645533;

// HashFields dos custom fields
const CUSTOM_1_HASH = "62d9f3ebece80bb7734b036d1ba3c4fc882926ee"; // Produto
const CUSTOM_2_HASH = "267b64c0013729c01cfad3b900d1da6374181869"; // Tipo de Cliente
const CUSTOM_3_HASH = "463aec8962f68ae13bab8d5c4d15d806eed93c5c"; // descrição1
const CUSTOM_4_HASH = "8143c980754f4f5a484404199c89645b6d2d79dc"; // descrição2
const CUSTOM_5_HASH = "1f6d821958695bdd002d58eec6c52c1440d91f26"; // descrição3
const CUSTOM_6_HASH = "aea4f34cf7ecc14d0f6abed7fab208104deff44f"; // descrição4
const CUSTOM_7_HASH = "9bfc2eda406580ca332b29fecc42b189a6a4a5f1"; // Grupo Funcional

// Função para obter token
async function getToken() {
  const query = `
    mutation {
      authenticationByClient(clientId: "${CLIENT_ID}", secret: "${SECRET}") {
        token
      }
    }
  `;
  const resp = await fetch(API_URL, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ query }),
  });
  const data = await resp.json();
  return data.data.authenticationByClient.token;
}

// Rota para criar atividade
app.post("/api/criar-atividade", async (req, res) => {
  try {
    const { title, customField } = req.body;
    const token = await getToken();

    const fixedFields = [
      `{ hashField: "${CUSTOM_1_HASH}", value: "Artia" }`,
      `{ hashField: "${CUSTOM_2_HASH}", value: "Clientes Externo" }`
    ];

    const userFields = (customField || [])
      .filter(f => f.value && f.value.trim() !== "")
      .map(f => `{ hashField: "${f.hashField}", value: "${f.value}" }`);

    const allFields = [...fixedFields, ...userFields].join(", ");

    const query = `
      mutation {
        createActivity(
          title: "${title}",
          folderId: ${FOLDER_ID},
          accountId: ${ACCOUNT_ID},
          responsibleId: ${RESPONSIBLE_ID},
          folderTypeId: ${FOLDER_TYPE_ID},
          customField: [${allFields}]
        ) {
          id
          title
        }
      }
    `;

    const response = await fetch(API_URL, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "Authorization": \`Bearer \${token}\`,
        "OrganizationId": ORG_ID,
      },
      body: JSON.stringify({ query }),
    });

    const result = await response.json();
    if (result.errors) throw new Error(result.errors.map(e => e.message).join(", "));

    res.json(result.data.createActivity);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

app.listen(80, () => console.log("Backend rodando em http://localhost:80"));
```

## /home/ticket/site/index.html

```
<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8" />
  <title>Criar Atividade - Artia</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', sans-serif; }
    body { background: #f5f7fa; display: flex; justify-content: center; align-items: flex-start; min-height: 100vh; padding: 40px 20px; color: #333; }
    .container { background: #fff; padding: 30px 40px; border-radius: 12px; box-shadow: 0 10px 25px rgba(0,0,0,0.1); width: 100%; max-width: 500px; animation: fadeIn 0.8s ease-out; }
    h1 { text-align: center; margin-bottom: 25px; color: #1f2937; }
    label { display: block; margin-bottom: 6px; font-weight: 600; }
    input[type="text"], select { width: 100%; padding: 10px 14px; border: 1px solid #ccc; border-radius: 8px; margin-bottom: 20px; transition: all 0.2s; font-size: 14px; }
    input[type="text"]:focus, select:focus { border-color: #00bebe; outline: none; box-shadow: 0 0 0 3px rgba(0,190,190,0.2); }
    button { width: 100%; background: #00bebe; color: #fff; font-weight: bold; border: none; padding: 12px; border-radius: 8px; cursor: pointer; transition: background 0.3s; font-size: 16px; }
    button:hover:not(:disabled) { background: #00a7a7; }
    button:disabled { opacity: 0.6; cursor: not-allowed; }
    #saida { padding: 15px; border-radius: 8px; background: #f3f4f6; min-height: 40px; transition: background 0.3s; }
    #saida.success { background: #d0f4f4; color: #006666; }
    #saida.error { background: #fee2e2; color: #991b1b; }
    @keyframes fadeIn { from { opacity: 0; transform: translateY(-20px); } to { opacity: 1; transform: translateY(0); } }
  </style>
</head>
<body>
  <div class="container">
    <h1>Criar Atividade</h1>

    <form id="form-atividade">
      <label for="title">Descrição:</label>
      <input id="title" name="title" type="text" required />

      <label for="custom3">Qual o processo que você segue hoje(Com ou sem o Artia)?</label>
      <input id="custom3" name="custom3" type="text" />

      <label for="custom4">Qual e o problema que será resolvido?</label>
      <input id="custom4" name="custom4" type="text" />

      <label for="custom5">Qual o impacto que essa situação a ser resolvida causa atualmente em sua operação?</label>
      <input id="custom5" name="custom5" type="text" />

      <label for="custom6">Qual a solução proposta?</label>
      <input id="custom6" name="custom6" type="text" />

      <label for="custom7">Grupo Funcional *</label>
      <select id="custom7" name="custom7" required>
        <option value="">Selecione...</option>
        <option value="Admin">Admin</option>
        <option value="Admin2">Admin2</option>
        <option value="Admin3">Admin3</option>
      </select>

      <button id="btn-enviar" type="submit">Enviar</button>
    </form>

    <hr>
    <div id="saida" aria-live="polite"></div>
  </div>

  <script>
    const form = document.getElementById('form-atividade');
    const saida = document.getElementById('saida');
    const btn = document.getElementById('btn-enviar');

    async function criarAtividade(data) {
      const resp = await fetch('/api/criar-atividade', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data),
      });
      const json = await resp.json();
      if (!resp.ok) throw new Error(json.error || 'Erro desconhecido');
      return json;
    }

    form.addEventListener('submit', async (e) => {
      e.preventDefault();
      saida.textContent = '';
      saida.className = '';
      btn.disabled = true;
      btn.textContent = 'Enviando...';

      const payload = {
        title: form.title.value.trim(),
        customField: [
          { hashField: "463aec8962f68ae13bab8d5c4d15d806eed93c5c", value: form.custom3.value },
          { hashField: "8143c980754f4f5a484404199c89645b6d2d79dc", value: form.custom4.value },
          { hashField: "1f6d821958695bdd002d58eec6c52c1440d91f26", value: form.custom5.value },
          { hashField: "aea4f34cf7ecc14d0f6abed7fab208104deff44f", value: form.custom6.value },
          { hashField: "9bfc2eda406580ca332b29fecc42b189a6a4a5f1", value: form.custom7.value }
        ]
      };

      try {
        const resultado = await criarAtividade(payload);
        saida.className = 'success';
        saida.innerHTML = `<strong>Atividade criada!</strong><br>ID: ${resultado.id}<br>Título: ${resultado.title}`;
      } catch (err) {
        saida.className = 'error';
        saida.innerHTML = `<strong>Erro:</strong> ${err.message}`;
      } finally {
        btn.disabled = false;
        btn.textContent = 'Enviar';
      }
    });
  </script>
</body>
</html>
```

## 

```
cd /home/ticket
docker compose down
docker compose build --no-cache
docker compose up -d
docker compose logs -f
```
