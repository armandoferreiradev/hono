### Documento Gerado: 04 - Guia de Implementação do Shadcn UI (Workflow Híbrido)

# 📄 Guia de Implementação: Shadcn UI (Workflow Híbrido)

## 1️⃣ Objetivo

Instruir o agente a executar todas as etapas de **automação** para preparar e implementar o Shadcn UI. O fluxo inclui pontos de pausa para a **intervenção manual e interativa** do usuário, que é indispensável.

## 2️⃣ Fluxo de Operação (Divisão de Tarefas)

O processo ocorrerá na seguinte ordem:

1.  **AGENTE - Passo 1:** Preparar o ambiente (configurar aliases de caminho).
2.  **USUÁRIO - Passo 2:** Executar o comando interativo `init`.
3.  **AGENTE - Passo 3:** Continuar com as tarefas não-interativas (adicionar e usar componentes).

## 3️⃣ Instruções para o Agente - PASSO 1 (Preparação)

Execute as seguintes modificações para preparar o projeto para o CLI do Shadcn UI.

**A. Instale as dependências de desenvolvimento:**

```bash
pnpm install -D @types/node
```

**B. Modifique o arquivo `tsconfig.json`:**
Adicione as chaves `baseUrl` e `paths` ao objeto `compilerOptions`.

```json
{
  "compilerOptions": {
    // ... outras opções existentes ...
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  }
}
```

**C. Modifique o arquivo `vite.config.ts`:**
Adicione a configuração de `resolve.alias`.

```typescript
import build from '@hono/vite-build/cloudflare-pages'
import devServer from '@hono/vite-dev-server'
import adapter from '@hono/vite-dev-server/cloudflare'
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'
import path from 'path'

export default defineConfig({
  plugins: [
    tailwindcss(),
    build(),
    devServer({
      adapter,
      entry: 'src/index.tsx'
    })
  ],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
})
```

## 4️⃣ Ponto de Pausa - Intervenção do Usuário (PASSO 2)

**AÇÃO DO AGENTE:** Neste ponto, a preparação está concluída. Pare a execução e notifique o usuário com a seguinte mensagem para que ele possa realizar a etapa manual.

> **Mensagem para o Usuário:**
>
> "Ambiente preparado para a inicialização do Shadcn UI. A próxima etapa é interativa e deve ser executada por você.
>
> **1. Execute o seguinte comando no seu terminal:**
>
> ```bash
> pnpm dlx shadcn@latest init
> ```
>
> **2. Responda aos prompts exatamente como planejado:**
>
>   * **TypeScript:** `yes`
>   * **Style:** `Default`
>   * **Global CSS:** `src/style.css`
>   * **CSS Variables:** `yes`
>   * **Tailwind Config:** `tailwind.config.ts`
>   * **Components Alias:** `@/components`
>   * **Utils Alias:** `@/lib/utils`
>   * **React Server Components:** `no`
>
> Após concluir, por favor, me informe para que eu possa prosseguir com a Fase 3."

## 5️⃣ Instruções para o Agente - PASSO 3 (Continuação)

**AÇÃO DO AGENTE:** Após receber a confirmação do usuário de que o passo 2 foi concluído, prossiga com as tarefas de automação.

**A. Adicione os componentes necessários (Exemplo):**

```bash
pnpm dlx shadcn@latest add card button input label
```

**B. Implemente a interface (Exemplo):**
Modifique o arquivo `src/index.tsx` para utilizar os componentes adicionados.

```tsx
import { Hono } from 'hono'
import { renderer } from './renderer'
import { Button } from '@/components/ui/button'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'

const app = new Hono()

app.get('/', (c) => {
  return c.render(
    <div class="p-8 bg-background text-foreground min-h-screen">
      <Card class="w-[350px]">
        <CardHeader>
          <CardTitle>Criar Projeto</CardTitle>
        </CardHeader>
        <CardContent>
          <form>
            <div class="grid w-full items-center gap-4">
              <div class="flex flex-col space-y-1.5">
                <Label htmlFor="name">Nome</Label>
                <Input id="name" placeholder="Nome do seu projeto" />
              </div>
              <Button>Enviar</Button>
            </div>
          </form>
        </CardContent>
      </Card>
    </div>
  )
})

export default app
```

-----
