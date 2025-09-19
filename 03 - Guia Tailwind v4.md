# Guia para Implementar Tailwind CSS v4 em Projetos Hono + Vite

Este guia descreve os passos para adicionar o Tailwind CSS v4 a um projeto existente que utiliza Hono e Vite para Server-Side Rendering (SSR).

## 1️⃣ Instalação

Instale o `tailwindcss` e o plugin `@tailwindcss/vite` como dependências de desenvolvimento:

```bash
pnpm install -D tailwindcss @tailwindcss/vite
```

## 2️⃣ Configuração do Vite

Modifique o arquivo `vite.config.ts` para incluir o plugin do Tailwind CSS.

```typescript
import build from '@hono/vite-build/cloudflare-pages'
import devServer from '@hono/vite-dev-server'
import adapter from '@hono/vite-dev-server/cloudflare'
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [
    tailwindcss(),
    build(),
    devServer({
      adapter,
      entry: 'src/index.tsx'
    })
  ]
})
```

## 3️⃣ Criação do CSS Principal

Crie um arquivo `src/style.css` e adicione a diretiva `@import` do Tailwind. Você pode também adicionar um bloco `@theme` para customizações.

```css
@import "tailwindcss";

@theme {
  /* Suas customizações de tema aqui */
}
```

## 4️⃣ Injeção do CSS e do Cliente Vite no Renderer

Modifique o arquivo `src/renderer.tsx` para importar o CSS como uma string (usando `?inline`) e injetá-lo em uma tag `<style>`. Adicione também o script do cliente Vite para habilitar o Hot Module Replacement (HMR).

```tsx
import { jsxRenderer } from 'hono/jsx-renderer'
import css from './style.css?inline'

export const renderer = jsxRenderer(({ children, title }) => {
  return (
    <html lang="en">
      <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>{title}</title>
        <script type="module" src="/@vite/client"></script>
        <style>{css}</style>
      </head>
      <body>
        {children}
      </body>
    </html>
  )
})
```

## 5️⃣ Utilização

Agora você pode usar as classes do Tailwind CSS em seus componentes JSX. O HMR do Vite irá recarregar os estilos automaticamente quando você fizer alterações.

---
