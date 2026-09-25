# 🗄️ Estrutura do projeto

## Visão geral

```
src/
  app/          → rotas e composição das features (camada mais alta)
  assets/       → imagens e SVGs compartilhados, importados no código
  components/   → componentes compartilhados
    ui/         → componentes shadcn e wrappers
    form/       → componentes de formulário integrados ao React Hook Form
    layout/     → navbars, sidebars, layouts
  config/       → variáveis de ambiente tipadas e constantes globais
  features/     → módulos por funcionalidade
  hooks/        → hooks compartilhados
  lib/          → bibliotecas pré-configuradas (api-client, query-client, authorization)
  server/       → código exclusivo do servidor (apenas Next.js)
  stores/       → stores globais (Zustand)
  testing/      → utilitários de teste e handlers MSW
  types/        → tipos de domínio compartilhados
  utils/        → funções puras compartilhadas
```

Em projetos React + Vite, `app/` contém o roteador e os providers, e a pasta `server/` não existe.

## Features

A maior parte do código fica em `features/`. Cada feature contém só o que é dela:

```
features/products/
  api/          → um arquivo por requisição (fetcher + hook)
  assets/       → imagens usadas só por esta feature
  components/
  hooks/
  stores/
  types/
  utils/
```

Crie apenas as pastas que a feature usa.

## Sem barrel files

Não crie `index.ts` de reexportação. Eles prejudicam o tree shaking e deixam a compilação mais lenta. Importe o arquivo diretamente:

```ts
// ✅
import { ProductCard } from "@/features/products/components/product-card";

// ❌
import { ProductCard } from "@/features/products";
```

## Fluxo unidirecional

O código flui em uma direção: **compartilhado → features → app**.

- Pastas compartilhadas não importam de `features` nem de `app`.
- **Uma feature nunca importa de outra feature.** Código usado por mais de uma sobe para uma pasta compartilhada, ou as features são compostas na página.
- `app/` pode importar de tudo.

```tsx
// app/produtos/[id]/page.tsx: a página compõe features diferentes
import { ProductDetails } from "@/features/products/components/product-details";
import { AddToCartButton } from "@/features/cart/components/add-to-cart-button";
```

Essas regras são garantidas pelo ESLint (ver [Padrões do projeto](02-project-standards.md)).

## Servidor e cliente (Next.js)

- Código exclusivo do servidor (banco, sessão, cookies, regras de negócio que alteram dados) fica em `src/server/`, com `import "server-only";` na primeira linha.
- Somente `app/` importa de `src/server/`.
- Route Handlers são finos: validam a entrada, chamam um serviço de `src/server/services/` e respondem.
