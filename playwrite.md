
```
const { chromium } = require('playwright');

(async () => {
  console.log('Iniciando o script de cadastro...');

  const browser = await chromium.launch({ headless: false, slowMo: 100 });
  const context = await browser.newContext();
  const page = await context.newPage();

  // ===== Etapa 1 =====
  console.log('Abrindo página de registro...');
  await page.goto('https://app2.artia.com/users/registration');

  console.log('Preenchendo formulário da primeira etapa...');
  await page.fill('input[name="userName"]', 'João Teste');
  await page.fill('input[name="userPhone"]', '11999998888');
  await page.fill('input[name="userEmail"]', 'joao.te666ste@example.com.br');
  await page.fill('input[name="userPassword"]', 'SenhaSegura123');

  console.log('Clicando no botão "Criar conta"...');
  await Promise.all([
    page.click('button[data-test-id="create-account"]'),
    page.waitForLoadState('networkidle'),
  ]);

  // ===== Etapa 2 =====
  console.log('Preenchendo formulário da segunda etapa...');

  // Campo de texto livre
  await page.fill('input[name="companyName"]', 'My Company');

  // Função genérica para preencher campos de pesquisa com autocomplete
  const preencherSearchInput = async (containerDataTestId, texto) => {
    const container = page.locator(`[data-test-id="${containerDataTestId}"]`);

    // Abrir dropdown
    await container.locator('svg[data-test-id="toggle-select-open-status"]').click();

    // Selecionar o input interno
    const inputPesquisa = container.locator('input[data-test-id="search-input"]');
    await inputPesquisa.waitFor({ state: 'visible' });

    // Digitar o texto que você quer buscar
    await inputPesquisa.fill(texto);

    // Esperar a opção aparecer
    await page.waitForSelector(`p.chakra-text:has-text("${texto}")`, { timeout: 5000 });

    // Clicar na opção correta
    await page.click(`p.chakra-text:has-text("${texto}")`);

    console.log(`Campo ${containerDataTestId} preenchido com "${texto}"`);
  };

  // Preencher todos os campos de seleção
  await preencherSearchInput('businessLine', 'Education and Teaching'); // Ramo de atuação
  await preencherSearchInput('employeesNumber', '0 - 1');                // Número de colaboradores
  await preencherSearchInput('department', 'Sales');                     // Departamento
  await preencherSearchInput('userJobTitle', 'Manager');                 // Seu cargo

  console.log('Todos os campos da segunda etapa preenchidos.');

  // Botão finalizar
  await page.click('button:has-text("Finish")');

  // Espera um pouco para ver o resultado
  await page.waitForTimeout(3000);

  console.log('Cadastro finalizado!');
  await browser.close();
})();
```

---

```
const { chromium } = require('playwright');

// 👉 Descomente quando quiser usar o faker
// const { faker } = require('@faker-js/faker');

(async () => {
  console.log('Iniciando o script de cadastro...');

  const browser = await chromium.launch({ headless: false, slowMo: 100 });
  const context = await browser.newContext();
  const page = await context.newPage();

  console.log('Abrindo página de registro...');
  await page.goto('https://app2.artia.com/users/registration');

  // ========= DADOS FIXOS =========
  const nome = 'João Teste';
  const telefone = '11999998888';
  const email = 'joao.teste@example.com';
  const senha = 'SenhaSegura123';

  // ========= MODO FAKER (descomente abaixo) =========
  /*
  const nome = faker.person.fullName();
  const telefone = faker.phone.number('119#######'); // gera um número SP fictício
  const email = faker.internet.email({ firstName: nome.split(' ')[0] }).toLowerCase();
  const senha = faker.internet.password({ length: 10 });

  console.log(`Usando dados faker:
    Nome: ${nome}
    Telefone: ${telefone}
    Email: ${email}
    Senha: ${senha}
  `);
  */

  console.log('Preenchendo formulário...');
  await page.fill('input[name="userName"]', nome);
  await page.fill('input[name="userPhone"]', telefone);
  await page.fill('input[name="userEmail"]', email);
  await page.fill('input[name="userPassword"]', senha);

  console.log('Clicando no botão "Criar conta"...');
  await page.click('button[data-test-id="create-account"]');

  // Esperar só para ver a próxima etapa carregando (sem avançar ainda)
  await page.waitForTimeout(3000);

  console.log('Script finalizado.');
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
const { chromium } = require('playwright');

(async () => {
  console.log('Iniciando o script de cadastro...');

  const browser = await chromium.launch({ headless: false, slowMo: 100 });
  const context = await browser.newContext();
  const page = await context.newPage();

  console.log('Abrindo página de registro...');
  await page.goto('https://app2.artia.com/users/registration');

  console.log('Preenchendo formulário...');
  await page.fill('input[name="userName"]', 'João Teste');
  await page.fill('input[name="userPhone"]', '11999998888');
  await page.fill('input[name="userEmail"]', 'joao.teste@example.com');
  await page.fill('input[name="userPassword"]', 'SenhaSegura123');

  console.log('Clicando no botão "Criar conta"...');
  await page.click('button[data-test-id="create-account"]');

  // Esperar só para ver a próxima etapa carregando (sem avançar ainda)
  await page.waitForTimeout(3000);

  console.log('Script finalizado.');
  await browser.close();
})();
```

---

```bash
npm install @faker-js/faker
```
