# Padrões pessoais (válidos para todos os projetos)

Regras completas e justificativas: repositório skillset. O `CLAUDE.md` de cada projeto complementa este arquivo e prevalece em caso de conflito.

## Stack padrão

React ou Next.js (App Router) + TypeScript strict, Tailwind + shadcn/ui, TanStack Query, Zustand, React Hook Form + Zod, ESLint (flat config) + Prettier + Husky + lint-staged, Vitest + Testing Library + MSW. Pergunte antes de instalar qualquer dependência fora dessa lista.

## Estrutura

- Pastas: `app`, `assets`, `components` (`ui`, `form`, `layout`), `config`, `features`, `hooks`, `lib`, `server` (só Next), `stores`, `testing`, `types`, `utils`.
- Features contêm só as pastas que usam: `api`, `components`, `hooks`, `stores`, `types`, `utils`.
- **Sem barrel files.** Nunca crie `index.ts` de reexportação. Importe o arquivo diretamente.
- **Fluxo unidirecional:** compartilhado → features → app. Uma feature nunca importa de outra. A composição acontece em `app/`.
- Código de servidor fica em `src/server/` com `import "server-only";`, e só `app/` importa dele.

## Código

- Alias `@/` → `src/`. Nada de `../../`.
- Identificadores em inglês. Português só em textos da interface, mensagens de erro e URLs.
- Rotas: a pasta em `app/` já tem o nome da URL em português (`app/quem-somos/page.tsx`), minúsculas, kebab-case, sem acento nem cedilha. Sem `rewrites` para traduzir. Componentes e features continuam em inglês.
- Arquivos e pastas em kebab-case. Componentes em PascalCase. Hooks com `use`.
- Exports nomeados. Sem `export default` (exceto arquivos especiais do framework) e sem `export *`.
- Sem `any`. Tipos de schema com `z.infer`.
- Enum na interface via mapa de labels, nunca o valor cru.
- Toda tela que busca dados trata loading, erro e vazio.
- Acessibilidade: HTML semântico, `label` em inputs, foco visível, teclado. Mobile first.

## Estado

- Local primeiro (`useState`).
- Context só para dados que mudam pouco (sessão, tema).
- Zustand para estado global de UI, sempre com seletores.
- Dados da API só no TanStack Query, nunca em store.
- Filtros e paginação compartilháveis na URL.

## API

- Um único `lib/api-client.ts` com tratamento central de erros.
- Um arquivo por requisição em `features/*/api/`, com fetcher, query options e hook juntos.

## Segurança

- Sessão em cookie httpOnly. Nunca token em localStorage.
- Autorização sempre verificada no servidor. Políticas de permissão como funções puras em `lib/authorization.ts`.
- Validação com Zod no servidor, com o mesmo schema do formulário.

## Erros

- `error.tsx` por área de rota. `not-found.tsx` para recurso inexistente ou sem permissão.

## Assets e imagens

- `public/` só para arquivos com URL fixa (favicon, ícones de PWA). Imagens usadas em componentes ficam em `src/assets/` (ou `features/*/assets/`) e são importadas.
- Fotos em WebP/AVIF e comprimidas. Ícones e ilustrações em SVG otimizado. Nomes em kebab-case.
- Next: sempre `next/image`, nunca `<img>`. Com `fill`, sempre `sizes`. Imagem principal acima da dobra carregada com prioridade (confira a API da versão instalada).
- Vite: `width`/`height`, `loading="lazy"` fora da primeira tela, `srcset` quando houver versões.
- `alt` descritivo. Imagem decorativa com `alt=""`.
- Ícones com lucide-react, importados individualmente. Botão só com ícone precisa de `aria-label`.
- Fontes com `next/font` (Next) ou Fontsource (Vite). No máximo duas famílias.

## next.config

- `next.config.ts` tipado com `NextConfig`. Só opções com motivo claro. Confira a documentação da versão instalada antes de adicionar opções.
- `poweredByHeader: false`, `images.formats` com AVIF e WebP, headers de segurança (`X-Content-Type-Options`, `Referrer-Policy`, `X-Frame-Options`, `Permissions-Policy`).
- `remotePatterns` só com domínios específicos. Nunca `hostname: "**"`.
- Variáveis de ambiente fora do `next.config`: `.env.local` + validação com Zod em `src/config/env.ts` + `.env.example` no repositório. Nunca `NEXT_PUBLIC_` em segredo.
- Nunca desativar lint ou checagem de tipos no build.
- Nunca executar scripts no `next.config` (`execSync`, geração de arquivos). Use hooks `predev`/`prebuild` no `package.json`.
- SVGR, quando usado, configurado no webpack e no Turbopack, restrito a `assets/icons/`.
- `serverActions.allowedOrigins` só se necessário, com domínios específicos. Nunca curingas amplos.

## Comentários

- Padrão: não comentar.
- Só para o porquê de algo não óbvio, em uma ou duas linhas.
- Nunca comentário HTML renderizado, código comentado, `console.log`, marcadores de seção ou JSDoc em código interno.

## Lint e commits

- `no-console` como erro (permitidos `warn` e `error`).
- Restrição de imports entre camadas e kebab-case garantidos pelo ESLint.
- Nunca use `eslint-disable` para contornar erro. Pergunte.
- Conventional Commits.

## Testes

- Foco em integração com Testing Library. Unitários para regras de negócio puras.
- API simulada com MSW, sem mockar `fetch`.
- Testar o que aparece na tela, não detalhes de implementação.

## Forma de trabalhar

- Uma etapa por vez. Ao terminar, rode lint e testes, resuma e espere confirmação.
- Pergunte antes de criar telas ou fluxos que não foram descritos.
- Código legível vale mais que código curto.
