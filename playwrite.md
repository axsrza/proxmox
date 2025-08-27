
```
const { chromium } = require('playwright');

(async () => {
  console.log('Iniciando o script de cadastro...');

  const browser = await chromium.launch({
    headless: false,
    slowMo: 200,
    args: ['--start-maximized'], // abre maximizado
  });

  const context = await browser.newContext({
    viewport: null, // permite usar o tamanho real da janela
  });

  const page = await context.newPage();

  // ===== Etapa 1 =====
  console.log('Abrindo página de registro...');
  await page.goto('https://app2.artia.com/users/registration');

  console.log('Preenchendo formulário da primeira etapa...');
  await page.fill('input[name="userName"]', 'João Silva');
  await page.fill('input[name="userPhone"]', '11999998876');
  await page.fill('input[name="userEmail"]', 'joao.testador123@silva.com.br');
  await page.fill('input[name="userPassword"]', 'SenhaSegura123');

  console.log('Clicando no botão "Criar conta"...');
  await Promise.all([
    page.click('button[data-test-id="create-account"]'),
    page.waitForLoadState('networkidle'),
  ]);

  // ===== Etapa 2 =====
  console.log('Preenchendo formulário da segunda etapa...');
  await page.fill('input[name="companyName"]', 'Empresa');

  // Função para preencher campos de autocomplete / dropdown customizado
  const preencherSearchInput = async (containerDataTestId, texto, opcaoExata) => {
    console.log(`Selecionando ${opcaoExata} em ${containerDataTestId}...`);

    // Abre o dropdown
    const toggle = page.locator(
      `[data-test-id="${containerDataTestId}"] svg[data-test-id="toggle-select-open-status"]`
    );
    await toggle.click();

    // Seleciona apenas o input visível no momento (se houver)
    const inputPesquisa = page.locator('input[data-test-id="search-input"]:visible');
    if (await inputPesquisa.count() > 0) {
      await inputPesquisa.fill('');
      await inputPesquisa.type(texto, { delay: 150 });
    }

    // Aguarda a lista renderizar
    await page.waitForTimeout(1000);

    // Clica na primeira opção visível correta
    const opcao = page.locator(`p.chakra-text:has-text("${opcaoExata}"):visible`);
    const count = await opcao.count();
    console.log(`Foram encontradas ${count} opções visíveis para "${opcaoExata}"`);
    if (count > 0) {
      await opcao.first().click();
    } else {
      throw new Error(`Não foi possível encontrar a opção "${opcaoExata}" em ${containerDataTestId}`);
    }
  };

  // ===== Preenchendo campos =====
  await preencherSearchInput('businessLine', 'Education', 'Education and Teaching');
  await preencherSearchInput('employeesNumber', '0', '0 - 1');
  await preencherSearchInput('department', 'Others', 'Others');
  await preencherSearchInput('userJobTitle', 'Student', 'Student');
  await preencherSearchInput('mainGoal', 'Client', 'Client Projects');

  // ===== Finalizar cadastro =====
  console.log('Clicando no botão "Finalize"...');
  await Promise.all([
    page.getByRole('button', { name: 'Finalize' }).click(),
    page.waitForLoadState('networkidle'),
  ]);

  console.log('Cadastro finalizado com sucesso!');
  await browser.close();
})();
```

---

```
const { chromium } = require('playwright');
const { faker } = require('@faker-js/faker');
const fs = require('fs'); // Para salvar no CSV

// Função para salvar email e senha no CSV
const salvarRegistroCSV = (email, senha) => {
  const arquivo = 'registros.csv';
  const linha = `${email},${senha}\n`;
  fs.appendFileSync(arquivo, linha, 'utf8');
  console.log(`Registro salvo no CSV: ${email}`);
};

(async () => {
  console.log('Iniciando o script de cadastro...');

  const browser = await chromium.launch({
    headless: false,
    slowMo: 200,
    args: ['--start-maximized'],
  });

  const context = await browser.newContext({
    viewport: null,
  });

  const page = await context.newPage();

  // ===== Etapa 1 =====
  console.log('Abrindo página de registro...');
  await page.goto('https://app2.artia.com/users/registration');

  // Gerando dados aleatórios
  const nome = faker.person.fullName(); // Nome completo
  const telefone = faker.phone.number('11########'); // Telefone com DDD 11
  const email = faker.internet.email({ firstName: nome.split(' ')[0], lastName: nome.split(' ')[1] });
  const senha = 'SenhaSegura123'; // Senha fixa ou pode gerar aleatória

  console.log('Preenchendo formulário da primeira etapa...');
  await page.fill('input[name="userName"]', nome);
  await page.fill('input[name="userPhone"]', telefone);
  await page.fill('input[name="userEmail"]', email);
  await page.fill('input[name="userPassword"]', senha);

  console.log('Clicando no botão "Criar conta"...');
  await Promise.all([
    page.click('button[data-test-id="create-account"]'),
    page.waitForLoadState('networkidle'),
  ]);

  // ===== Etapa 2 =====
  console.log('Preenchendo formulário da segunda etapa...');
  const empresa = faker.company.name(); // Nome de empresa aleatório
  await page.fill('input[name="companyName"]', empresa);

  // Função para preencher campos de autocomplete / dropdown customizado
  const preencherSearchInput = async (containerDataTestId, texto, opcaoExata) => {
    console.log(`Selecionando ${opcaoExata} em ${containerDataTestId}...`);
    const toggle = page.locator(
      `[data-test-id="${containerDataTestId}"] svg[data-test-id="toggle-select-open-status"]`
    );
    await toggle.click();

    const inputPesquisa = page.locator('input[data-test-id="search-input"]:visible');
    if (await inputPesquisa.count() > 0) {
      await inputPesquisa.fill('');
      await inputPesquisa.type(texto, { delay: 150 });
    }

    await page.waitForTimeout(1000);

    const opcao = page.locator(`p.chakra-text:has-text("${opcaoExata}"):visible`);
    const count = await opcao.count();
    console.log(`Foram encontradas ${count} opções visíveis para "${opcaoExata}"`);
    if (count > 0) {
      await opcao.first().click();
    } else {
      throw new Error(`Não foi possível encontrar a opção "${opcaoExata}" em ${containerDataTestId}`);
    }
  };

  // ===== Preenchendo campos =====
  await preencherSearchInput('businessLine', 'Education', 'Education and Teaching');
  await preencherSearchInput('employeesNumber', '0', '0 - 1');
  await preencherSearchInput('department', 'Others', 'Others');
  await preencherSearchInput('userJobTitle', 'Student', 'Student');
  await preencherSearchInput('mainGoal', 'Client', 'Client Projects');

  // ===== Finalizar cadastro =====
  console.log('Clicando no botão "Finalize"...');
  await Promise.all([
    page.getByRole('button', { name: 'Finalize' }).click(),
    page.waitForLoadState('networkidle'),
  ]);

  console.log('Cadastro finalizado com sucesso!');

  // Salva email e senha no CSV
  salvarRegistroCSV(email, senha);

  await browser.close();
})();

```

---

```
const { chromium } = require('playwright');

(async () => {
  console.log('Iniciando o script...');

  const browser = await chromium.launch({ headless: false, slowMo: 100 });
  console.log('Navegador aberto.');

  const context = await browser.newContext({
    userAgent: 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, como Gecko) Chrome/116.0.0.0 Safari/537.36',
    viewport: { width: 1280, height: 720 }
  });

  const page = await context.newPage();

  console.log('Abrindo a página inicial do DuckDuckGo...');
  await page.goto('https://duckduckgo.com');
  console.log('Página inicial carregada.');

  console.log('Preenchendo o campo de busca com "brasil"...');
  await page.fill('input[name="q"]', 'brasil');
  console.log('Campo de busca preenchido.');

  console.log('Pressionando Enter e aguardando os resultados carregarem...');
  await Promise.all([
    page.keyboard.press('Enter'),
    page.waitForLoadState('networkidle')
  ]);
  console.log('Resultados carregados.');

  // Fechar o modal "Add DuckDuckGo to Chrome"
  try {
    console.log('Aguardando e tentando fechar o modal principal...');
    await page.waitForSelector('.js-badge-link-dismiss', { timeout: 10000 });
    await page.click('.js-badge-link-dismiss');
    console.log('Modal principal fechado.');
  } catch {
    console.log('Modal principal não apareceu.');
  }

  // Fechar o modal de cookies
  try {
    console.log('Aguardando e tentando fechar o modal de cookies...');
    await page.waitForSelector('.js-badge-link-close', { timeout: 5000 });
    await page.click('.js-badge-link-close');
    console.log('Modal de cookies fechado.');
  } catch {
    console.log('Modal de cookies não apareceu.');
  }

  console.log('Aguardando os resultados...');
  await page.waitForSelector('a.result__a');
  console.log('Resultados visíveis.');

  console.log('Tirando screenshot...');
  await page.screenshot({ path: 'resultado-duckduckgo.png' });
  console.log('Screenshot salva: resultado-duckduckgo.png');

  await browser.close();
  console.log('Navegador fechado. Script finalizado com sucesso!');
})();
```

---

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Rodar DuckDuckGo Script",
      "program": "${workspaceFolder}/duckduckgo-busca.test.js",
      "skipFiles": ["<node_internals>/**"],
      "console": "integratedTerminal"
    },
    {
      "type": "node",
      "request": "launch",
      "name": "Rodar Cadastro Artia",
      "program": "${workspaceFolder}/cadastro-artia.js",
      "skipFiles": ["<node_internals>/**"],
      "console": "integratedTerminal"
    }
  ]
}

```

---

```bash
nano .vscode/launch.json
```

---

```
nano cadastro-artia.js
```

---

```bash
npm install @faker-js/faker
```

---

```bash
nano duckduckgo-busca.test.js
```

---

























---

# Playwright: Tornando Cadastro Mais Humano e Aleatório

## 1️⃣ Pontos que podem ser manipulados

| Ponto                           | Pode ser aleatório? | Como fazer                     |
| ------------------------------- | ------------------- | ------------------------------ |
| IP                              | ❌ Não pelo script   | VPN / proxy                    |
| User-Agent                      | ✅                   | Lista de userAgents aleatórios |
| Cookies / localStorage          | ✅                   | Criar valores aleatórios       |
| Viewport                        | ✅                   | Largura e altura aleatórias    |
| Movimentos e tempo de interação | ✅                   | Delays e mouse.move aleatórios |

## 2️⃣ User-Agent aleatório

```js
const userAgents = [
  'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 Chrome/139.0.0.0 Safari/537.36',
  'Mozilla/5.0 (Macintosh; Intel Mac OS X 13_6) AppleWebKit/605.1.15 Safari/605.1.15',
  'Mozilla/5.0 (X11; Linux x86_64) Gecko/20100101 Firefox/117.0'
];
const userAgent = userAgents[Math.floor(Math.random() * userAgents.length)];

const context = await browser.newContext({
  viewport: null,
  userAgent: userAgent
});
```

## 3️⃣ Cookies / localStorage aleatórios

```js
await context.addCookies([
  { name: 'session_id', value: faker.string.alphanumeric(16), domain: 'app2.artia.com', path: '/' }
]);
```

## 4️⃣ Viewport aleatório

```js
const width = faker.number.int({ min: 1024, max: 1920 });
const height = faker.number.int({ min: 768, max: 1080 });

const context = await browser.newContext({
  viewport: { width, height }
});
```

## 5️⃣ Movimentos e tempo de interação aleatório

```js
// Digitação com delays aleatórios
await page.type('input[name="userName"]', nome, { delay: faker.number.int({ min: 50, max: 200 }) });

// Movimentos de mouse aleatórios
const x = faker.number.int({ min: 100, max: 800 });
const y = faker.number.int({ min: 100, max: 600 });
await page.mouse.move(x, y);
await page.mouse.click(x, y);

// Pausas aleatórias entre ações
await page.waitForTimeout(faker.number.int({ min: 500, max: 2000 }));
```

## 🔹 Resumo

* IP → não controlável pelo script, requer VPN/proxy.
* User-Agent → aleatório a cada execução.
* Cookies → aleatórios, criados por script.
* Viewport → aleatório.
* Interação → delays, cliques e movimentos do mouse aleatórios.

Pode-se combinar todos esses pontos para tornar cada execução mais humana e menos detectável como bot.

---






// Lista de proxies gratuitos
const proxies = [
  '103.94.52.70:3128',  
  '139.99.237.62:80',
  '65.108.203.37:28080',
  '152.53.39.66:8041',
  '123.30.154.171:7777'
  // ...adicione quantos quiser https://free-proxy-list.net/pt/
];

// Escolhe um proxy aleatório
const proxyServer = proxies[Math.floor(Math.random() * proxies.length)];
console.log(`Usando proxy: ${proxyServer}`);

// Cria contexto do navegador usando o proxy
const context = await browser.newContext({
  viewport: null,
  proxy: { server: `http://${proxyServer}` }
});

const page = await context.newPage();
