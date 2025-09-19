# Guia para Agentes de IA – Hono + Cloudflare Pages

## 1️⃣ Objetivo

Olá, agente! 🤖
Este guia é seu ponto de partida para **criar aplicações simples e médias** usando Hono + Cloudflare Pages.

Você terá liberdade para:

* Criar landing pages, SPAs simples, dashboards ou micro-SaaS.
* Definir rotas GET/POST e rotas dinâmicas.
* Implementar SSR (Server-Side Rendering) com JSX.
* Consumir APIs externas.
* Usar Cloudflare D1 (relacional), KV ou R2 para armazenamento.

---

## 2️⃣ Ambiente Preparado

O ambiente que você recebe já possui:

* Node.js + pnpm
* Hono.js (backend + SSR)
* React + JSX (frontend)
* Vite.js (build, bundling e hot reload)
* Wrangler CLI autenticado com Cloudflare via OAuth
* Projeto inicial com template `cloudflare-pages`
* Estrutura mínima pronta:

```
my-app/
├── src/
│   ├── index.tsx        # backend + rotas
│   └── renderer.tsx     # template SSR
├── vite.config.ts       # build / frontend
├── wrangler.jsonc       # configuração Cloudflare
└── package.json         # scripts e dependências
```

---

## 3️⃣ Comandos Essenciais

| Objetivo              | Comando                                                             |
| --------------------- | ------------------------------------------------------------------- |
| Instalar dependências | `pnpm install`                                                      |
| Rodar ambiente local  | `pnpm dev`                                                          |
| Build + deploy        | `pnpm run deploy`                                                   |
| Consultar banco D1    | `pnpm wrangler d1 execute <db> --command="SELECT * FROM messages;"` |
| Validar autenticação  | `wrangler whoami`                                                   |

**Observação:** Sempre use `pnpm run deploy` para enviar a aplicação; `pnpm deploy` não funciona corretamente.

---

## 4️⃣ Banco de Dados D1

1. Criar banco:

```bash
pnpm wrangler d1 create portfolio-db
```

2. Criar schema inicial (`schema.sql`):

```sql
CREATE TABLE messages (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT,
  email TEXT,
  message TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

3. Executar schema local e remoto:

```bash
pnpm wrangler d1 execute portfolio-db --local --file=schema.sql
pnpm wrangler d1 execute portfolio-db --remote --file=schema.sql
```

4. Acessar dados via binding em `index.tsx`:

```ts
const { results } = await c.env.DB.prepare('SELECT * FROM messages').all();
```

---

## 5️⃣ Rotas e SSR

* **GET** e **POST** simples:

```ts
app.get('/', (c) => c.render(<h1>Hello, world!</h1>));
app.post('/contact', async (c) => {
  const data = await c.req.parseBody();
  await c.env.DB.prepare('INSERT INTO messages(name,email,message) VALUES (?,?,?)')
    .bind(data.name, data.email, data.message)
    .run();
  return c.json({ success: true });
});
```

* **Rotas dinâmicas:**

```ts
app.get('/products/:id', async (c) => {
  const id = c.req.param('id');
  // lógica
});
```

* **SSR via JSX:** sempre usar `c.render()` com `renderer.tsx`.

---

## 6️⃣ Bindings Disponíveis

* **D1Database:** banco relacional (SQLite)
* **KVNamespace:** key-value storage
* **R2Bucket:** object storage para arquivos grandes
* **Variables:** configuração de variáveis de ambiente

**Uso em rota Hono:**

```ts
const app = new Hono<{ Bindings: { DB: D1Database, MY_KV: KVNamespace } }>();
```

---

## 7️⃣ Consumo de APIs Externas

Você pode chamar qualquer API pública ou privada com `fetch()`:

```ts
const res = await fetch('https://api.example.com/data');
const data = await res.json();
return c.json(data);
```

---

## 8️⃣ Boas Práticas

1. Teste local antes de deploy (`pnpm dev`).
2. Confirme bindings no `wrangler.jsonc`.
3. Use SSR para páginas dinâmicas.
4. Use rotas POST para formularios ou inserção de dados.
5. Evite excesso de lógica pesada no Worker; use APIs externas se necessário.
6. Valide autenticação com `wrangler whoami`.

---
