# Documentação do Projeto Hono + Cloudflare Pages (para Agentes de IA)

## 1. Objetivo

Olá, agente! 🤖
Você será responsável por **criar, manter e evoluir aplicações simples e médias** usando Hono + Cloudflare Pages.
Seu papel inclui:

* Criar landing pages, formulários, catálogos ou pequenos dashboards.
* Implementar rotas dinâmicas e APIs simples.
* Integrar armazenamento usando Cloudflare D1 (relacional), KV ou R2.
* Gerenciar SSR (Server-Side Rendering) usando React/JSX.
* Garantir que o projeto funcione localmente e faça deploy correto na Cloudflare Pages.

Este documento define **ambiente, ferramentas, convenções e boas práticas** que você deve seguir.

---

## 2. Ambiente de Desenvolvimento

O projeto funciona com a seguinte base:

* **Gerenciador de pacotes:** `pnpm`
* **Framework Backend:** Hono.js
* **Framework Frontend:** React + JSX
* **Renderização:** SSR com `hono/jsx-renderer`
* **Build Tool:** Vite.js (configurado para SSR e assets client-side)
* **Plataforma de Deploy:** Cloudflare Pages
* **Banco de Dados:** Cloudflare D1 (SQLite), KV e R2 disponíveis
* **Autenticação Wrangler:** OAuth via `wrangler login` (persistente)

**Nota:** Você pode criar e gerenciar storages, bancos e rotas, consumindo APIs externas usando `fetch()`.

---

## 3. Comandos Essenciais

### 3.1. Instalação de Dependências

```bash
pnpm install
```

### 3.2. Desenvolvimento Local

```bash
pnpm dev
```

* Executa `wrangler pages dev ./dist` internamente.
* Servidor local com hot reload para backend Hono e frontend React.

### 3.3. Banco de Dados (D1)

* **Criar ou atualizar schema:**

  ```bash
  pnpm wrangler d1 execute <database_name> --local --file=schema.sql
  pnpm wrangler d1 execute <database_name> --remote --file=schema.sql
  ```

* **Consultar dados para debug:**

  ```bash
  pnpm wrangler d1 execute <database_name> --local --command="SELECT * FROM messages;"
  ```

### 3.4. Deploy para Cloudflare Pages

```bash
pnpm run deploy
```

* Executa build (`vite build`) e deploy (`wrangler pages deploy ./dist`).
* **Atenção:** sempre usar `pnpm run deploy`, não `pnpm deploy`.

---

## 4. Estrutura de Arquivos e Convenções

* **`wrangler.jsonc`** → Configuração Cloudflare, bindings de D1/KV/R2, deploy.
* **`src/index.tsx`** → Ponto de entrada do Hono:

  * Definir rotas GET/POST.
  * Acessar bindings via `c.env`:

    ```ts
    const { results } = await c.env.DB.prepare('SELECT * FROM messages').all();
    ```
* **`src/renderer.tsx`** → Template base SSR:

  * Define `<html>`, `<head>`, `<body>`
  * Inclui folhas de estilo globais e scripts client-side.

---

## 5. Tecnologias Disponíveis para o Agente

Você pode usar:

* **Hono:** Rotas, middleware, SSR.
* **React + JSX:** Componentes de frontend.
* **Vite:** Build e hot reload.
* **D1:** Banco SQLite para dados relacionais simples.
* **KV:** Key-Value para storage leve.
* **R2:** Object storage para arquivos grandes ou assets.
* **Fetch / APIs externas:** Consumir qualquer API pública ou privada.

**Observação:** Você pode criar rotas dinâmicas, endpoints REST, SSR e consumo de APIs sem restrições.

---

## 6. Boas Práticas de Desenvolvimento

1. **Rotas e SSR**

   * Sempre defina tipos de bindings:

     ```ts
     type Bindings = { DB: D1Database }
     const app = new Hono<{ Bindings: Bindings }>()
     ```
   * Use `c.render()` para enviar HTML gerado via JSX.

2. **Formulários e APIs**

   * Parse do body de formulários:

     ```ts
     const data = await c.req.parseBody()
     ```
   * Inserção no banco D1:

     ```ts
     await c.env.DB.prepare('INSERT INTO messages(name,email) VALUES (?,?)').bind(data.name, data.email).run()
     ```

3. **Rotas dinâmicas**

   * Exemplos:

     ```ts
     app.get('/products/:id', async (c) => { ... })
     app.post('/api/contact', async (c) => { ... })
     ```

4. **Consumindo APIs externas**

   ```ts
   const res = await fetch('https://api.example.com/data')
   const data = await res.json()
   return c.json(data)
   ```

5. **Deploy e testes**

   * Teste local: `pnpm dev`
   * Deploy: `pnpm run deploy`
   * Sempre valide `wrangler whoami` para confirmar autenticação.

---

## 7. Exemplo de Workflow Inicial do Agente

**Tarefa 1:** Criar landing page mínima (`index.tsx`)

* Header, main, footer, formulário simples

**Tarefa 2:** Configurar banco D1 e tabela `messages`

* Criar schema `schema.sql`
* Executar local e remoto

**Tarefa 3:** Implementar rota POST `/contact` para inserir dados no banco

**Tarefa 4:** Implementar rota GET `/messages` para listar registros

**Tarefa 5:** Testar local (`pnpm dev`) e fazer deploy (`pnpm run deploy`)

**Tarefa 6:** Expandir conforme necessidade (rotas dinâmicas, APIs externas, KV, R2)

---

## 8. Observações Finais para o Agente

* Esta base **não é limitada a sites estáticos**.
* Suporta **dinamicidade, rotas, SSR, APIs e banco de dados**.
* Ideal para **MVPs, landing pages, catálogos ou dashboards simples a médios**.
* Tudo deve ser feito de forma incremental, seguindo a sequência de tarefas, validando cada passo antes de prosseguir.

---
