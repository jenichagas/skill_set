# 🔧 Configuração do Next.js

O `next.config` deve ser curto: só o que o projeto realmente precisa. Cada opção adicionada precisa ter um motivo.

> As opções do Next mudam entre versões (algumas saem de `experimental`, outras são removidas). Antes de adicionar uma opção, confira a documentação da versão instalada.

## Arquivo em TypeScript

Use `next.config.ts`, com o tipo `NextConfig`, para ter autocomplete e erro de tipo em opções inválidas.

## Configuração base

```ts
// next.config.ts
import type { NextConfig } from "next";

const securityHeaders = [
  { key: "X-Content-Type-Options", value: "nosniff" },
  { key: "Referrer-Policy", value: "strict-origin-when-cross-origin" },
  { key: "X-Frame-Options", value: "DENY" },
  { key: "Permissions-Policy", value: "camera=(), microphone=(), geolocation=()" },
];

const nextConfig: NextConfig = {
  poweredByHeader: false,
  images: {
    formats: ["image/avif", "image/webp"],
    remotePatterns: [{ protocol: "https", hostname: "images.example.com" }],
  },
  async headers() {
    return [{ source: "/(.*)", headers: securityHeaders }];
  },
};

export default nextConfig;
```

## O que cada parte faz

**`poweredByHeader: false`**: remove o header `X-Powered-By: Next.js`, que só serve para informar a quem estiver atacando qual tecnologia o site usa.

**`images.formats`**: entrega AVIF quando o navegador suporta, com WebP como alternativa. Arquivos bem menores que JPEG e PNG.

**`images.remotePatterns`**: lista os domínios de onde o `next/image` pode carregar imagens externas.

- Libere **domínios específicos**, nunca um curinga como `hostname: "**"`. Um curinga permite que qualquer pessoa use o otimizador de imagens do seu servidor para qualquer imagem da internet.
- Quando possível, restrinja também o caminho com `pathname` (ex.: `"/uploads/**"`).

**`headers()`**: aplica headers de segurança em todas as respostas.

| Header | Protege contra |
|---|---|
| `X-Content-Type-Options: nosniff` | O navegador "adivinhar" o tipo de um arquivo e executar algo que não deveria |
| `Referrer-Policy` | Vazar a URL completa (com parâmetros) para outros sites |
| `X-Frame-Options: DENY` | O site ser carregado dentro de um iframe em outro domínio (clickjacking) |
| `Permissions-Policy` | Acesso a câmera, microfone e localização sem necessidade. Libere só o que o app usa |

Um **Content Security Policy (CSP)** completo dá mais proteção, mas é mais complexo de configurar no Next (envolve nonce gerado no `proxy.ts`). Adicione quando o projeto for para produção real, seguindo o guia oficial do Next.

## Opções para avaliar por projeto

- **Rotas tipadas** (`typedRoutes`): o TypeScript acusa erro em `<Link href="/rota-que-nao-existe">`. Vale ativar se a versão instalada suportar de forma estável.
- **`output: "standalone"`**: gera um build mínimo para rodar em Docker. Desnecessário em deploy na Vercel.
- **Bundle analyzer** (`@next/bundle-analyzer`): mostra o que está pesando no bundle. Ative só quando for investigar tamanho, via variável de ambiente.
- **`redirects()`**: para URLs antigas que mudaram de lugar, com `permanent: true` quando a mudança for definitiva.

## SVG como componente (SVGR)

Para importar SVG como componente React (`import CloseIcon from "@/assets/icons/close.svg"`), configure o `@svgr/webpack` **nos dois bundlers**, com o mesmo escopo. Se configurar só o webpack, o dev com Turbopack e o build se comportam de formas diferentes.

```ts
const nextConfig: NextConfig = {
  webpack(config) {
    config.module.rules.push({
      test: /\/assets\/icons\/.*\.svg$/,
      use: ["@svgr/webpack"],
    });
    return config;
  },
  turbopack: {
    rules: {
      "**/assets/icons/*.svg": {
        loaders: ["@svgr/webpack"],
        as: "*.js",
      },
    },
  },
};
```

Restrinja a regra a uma pasta (`assets/icons/`). Os demais SVGs continuam sendo importados como arquivo, para uso no `next/image`. Adicione também a declaração de tipos (`declare module "*.svg"`) para o TypeScript aceitar o import.

## Scripts de geração ficam no `package.json`

O `next.config` só declara configuração. **Nunca execute scripts nele** (`execSync`, geração de arquivos, chamadas de rede). Ele é carregado várias vezes, por mais de um processo, e o script roda repetidamente.

Scripts que precisam rodar antes do dev e do build usam os hooks `pre*` do npm:

```json
"scripts": {
  "icons": "node scripts/generate-icon-manifest.mjs",
  "predev": "npm run icons",
  "prebuild": "npm run icons",
  "dev": "next dev",
  "build": "next build"
}
```

## Server Actions: `allowedOrigins`

Define de quais origens uma Server Action aceita chamadas. É a proteção contra CSRF.

- Normalmente **não é necessário**: por padrão, o Next já aceita a própria origem.
- Só adicione quando o app ficar atrás de um proxy ou domínio diferente, listando **domínios específicos**.
- Nunca use curingas amplos como `"*.vercel.app"`, que liberam qualquer projeto hospedado na Vercel.

## Rotas traduzidas com `rewrites`

Para projetos novos, o padrão é criar as pastas de rota já em português (ver [Padrões do projeto](02-project-standards.md#rotas)). Este padrão é para projetos que já têm pastas em inglês.

Centralize as rotas em um mapa e gere `rewrites` e `redirects` a partir dele. O redirect leva a URL em inglês para a portuguesa, evitando conteúdo duplicado:

```ts
const localizedRoutes = {
  "/quem-somos": "/about",
  "/contato": "/contact",
  "/produtos/:slug": "/products/:slug",
  "/produtos": "/products",
} as const;

const nextConfig: NextConfig = {
  rewrites: async () =>
    Object.entries(localizedRoutes).map(([source, destination]) => ({ source, destination })),
  redirects: async () =>
    Object.entries(localizedRoutes).map(([source, destination]) => ({
      source: destination,
      destination: source,
      permanent: true,
    })),
};
```

Os links do app sempre usam a URL em português. Teste bem antes de publicar, porque mudanças em redirects afetam o SEO.

## Detalhes de `images`

- **`qualities`:** declare as qualidades usadas nos componentes (ex.: `[75, 85]`). Nas versões recentes, valores fora da lista são recusados.
- **`minimumCacheTTL`:** um valor alto (ex.: 30 dias) é bom para imagens que não mudam. Se uma imagem for substituída mantendo o mesmo nome, ela demora a atualizar. Prefira nomes novos a cada troca.
- **AVIF + WebP ou só WebP:** o AVIF gera arquivos menores, mas é mais lento para codificar e consome mais processamento no servidor. As duas escolhas são válidas, desde que conscientes.
- Omita `port: ""` em `remotePatterns`. Já é o padrão.

## O que não vai no `next.config`

- **Variáveis de ambiente.** Ficam em `.env.local` (fora do Git) e são validadas com Zod em `src/config/env.ts`, para o app falhar logo ao iniciar se faltar alguma:

```ts
// src/config/env.ts
import { z } from "zod";

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  NEXT_PUBLIC_API_URL: z.string().url(),
});

export const env = envSchema.parse(process.env);
```

- Variáveis com prefixo **`NEXT_PUBLIC_`** vão para o navegador e ficam visíveis para qualquer pessoa. Nunca use esse prefixo em chaves secretas.
- Mantenha um **`.env.example`** no repositório, com os nomes das variáveis e valores fictícios.
- **Regras de lint.** Ficam no `eslint.config.mjs`. Não desative o lint ou a checagem de tipos no build para "fazer passar".
