# Documentação de Setup do Ambiente para Agente de IA

## 1. Objetivo

Preparar o ambiente local para que o agente de IA possa criar e gerenciar projetos Hono + Cloudflare Pages com SSR, rotas dinâmicas e banco de dados (D1/KV/R2).

---

## 2. Pré-requisitos

1. **Node.js** instalado (versão compatível com Hono e Vite).
2. **Gerenciador de pacotes:** `pnpm` (ou npm/yarn se preferir).
3. **Wrangler CLI** instalado globalmente:

   ```bash
   npm install -g wrangler
   ```
4. **Conta Cloudflare** ativa.
5. **Autenticação Wrangler** via OAuth:

   ```bash
   wrangler login --browser=false
   ```

   * Copie o link gerado no terminal e faça login.
   * Confirme com:

   ```bash
   wrangler whoami
   ```

   * Deve exibir email, conta e escopos autorizados.

---

## 3. Inicialização do Projeto

1. Criar projeto Hono usando template `cloudflare-pages`:

   ```bash
   pnpm create hono@latest my-app
   cd my-app
   pnpm install
   ```

2. Validar arquivos principais:

   * `src/index.tsx` → backend + rotas
   * `src/renderer.tsx` → template SSR
   * `vite.config.ts` → configuração de build/frontend
   * `wrangler.jsonc` → configuração Cloudflare Pages e bindings
   * `package.json` → scripts e dependências

---
