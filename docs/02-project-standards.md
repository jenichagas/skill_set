# ⚙️ Padrões do projeto

## TypeScript

- Modo `strict`. Sem `any`.
- Tipos derivados de schemas com `z.infer`.
- `import type` / `export type` para tipos.

## Imports absolutos

Alias único `@/` apontando para `src/`:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": { "@/*": ["./src/*"] }
  }
}
```

Nada de `../../../`.

## Nomes

- **Todo identificador de código em inglês:** variáveis, funções, tipos, campos, valores de enum.
- **Português só no que o usuário vê:** textos da interface, mensagens de erro e URLs (ver [Rotas](#rotas)).
- Valores de enum aparecem na interface por um mapa de labels: `statusLabels = { pending: "Pendente", paid: "Pago" }`.
- **Arquivos e pastas em kebab-case:** `product-card.tsx`, `get-products.ts`, `use-debounce.ts`. É o padrão do shadcn. O componente dentro do arquivo continua em PascalCase.
- Hooks começam com `use`.

## Rotas

Para público brasileiro, **URLs em português**, que ajudam no SEO e são mais fáceis de entender e compartilhar.

- **A pasta de rota já tem o nome da URL em português:** `app/quem-somos/page.tsx` gera `/quem-somos`. Sem `rewrites` para traduzir.
- Minúsculas, kebab-case, **sem acento e sem cedilha:** `/politica-de-privacidade`, `/solucoes-financeiras`.
- Query params em inglês, porque são lidos pelo código: `?search=`, `?page=`.
- Nomes de componentes e arquivos **dentro** da rota continuam em inglês: `app/quem-somos/page.tsx` exporta `AboutPage`, e os componentes vêm de `features/about/`.

**Exceção: site multilíngue.** Aí as rotas usam um segmento de idioma (`app/[locale]/...`) com uma estrutura de i18n, e a tradução dos caminhos é feita por ela.

**Projeto existente com pastas em inglês + `rewrites`:** não migre só por isso. Mantenha o mapa de rotas centralizado e redirecione a versão em inglês para a portuguesa, para não haver conteúdo duplicado (ver [Configuração do Next.js](12-next-config.md#rotas-traduzidas-com-rewrites)).

## Exports

- Sempre nomeados. Sem `export default` e sem `export *`.
- Exceção: arquivos especiais do framework (`page.tsx`, `layout.tsx`, `error.tsx`, `route.ts`...).

## ESLint

Flat config (`eslint.config.mjs`). Além das regras padrão do framework:

```js
"no-console": ["error", { allow: ["warn", "error"] }],
```

**Restrição de imports entre camadas** (`import/no-restricted-paths` ou equivalente):

- uma feature não importa de outra;
- `features` não importa de `app`;
- pastas compartilhadas não importam de `features` nem de `app`;
- nada fora de `app` importa de `server`.

**Nomes de arquivo e pasta** com `eslint-plugin-check-file`, em kebab-case. No Next, as pastas de `src/app/` ficam fora da regra de pastas, por causa de `[id]` e `(grupo)`.

Nunca use `eslint-disable` para contornar um erro.

## Prettier

Formatação automática ao salvar e no pré-commit.

## Husky + lint-staged

A cada commit, `eslint --fix` e `prettier --write` rodam nos arquivos alterados. Se falhar, o commit é bloqueado.

## Commits

Conventional Commits: `feat:`, `fix:`, `refactor:`, `test:`, `docs:`, `chore:`.
