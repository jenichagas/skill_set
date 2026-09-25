# 🖼️ Assets e imagens

## Onde ficam

| Tipo | Onde | Por quê |
|---|---|---|
| Favicon, ícones de PWA, `robots.txt`, arquivos com URL fixa | `public/` | Servidos como estão, pela URL (`/icons/icon-192.png`) |
| Imagens e SVGs usados em componentes compartilhados | `src/assets/` | Importados no código, com hash no nome e cache longo |
| Imagens usadas por uma única feature | `src/features/*/assets/` | Colocation |

Nomes em kebab-case: `hero-banner.webp`, `empty-state.svg`.

## Formatos

- **SVG** para logos, ilustrações simples e ícones. Otimize com **SVGO** (ou pela interface do SVGOMG) antes de adicionar ao projeto.
- **WebP** (ou **AVIF**) para fotos.
- **PNG** apenas quando precisar de transparência e o SVG não servir.
- Nunca subir fotos direto da câmera ou do Figma sem comprimir. Use o **Squoosh** para converter e comprimir.

## Tamanhos

- Exporte a imagem no maior tamanho em que ela aparece na tela (considerando telas de alta densidade, até 2x), nunca maior.
- Referência para fotos: até ~200 KB cada. Imagem de destaque (hero): até ~300 KB.
- Imagens decorativas grandes devem ser as primeiras candidatas a corte ou compressão.

## Next.js: `next/image`

Use sempre `next/image` no lugar de `<img>`. Ele gera versões redimensionadas, entrega formatos modernos e aplica lazy loading automaticamente.

```tsx
import Image from "next/image";
import heroBanner from "@/assets/hero-banner.webp";

<Image src={heroBanner} alt="Pessoa trabalhando em um notebook sobre uma mesa de madeira" placeholder="blur" />
```

- **Imagens importadas** já informam largura e altura sozinhas.
- **Imagens remotas** exigem `width` e `height`, ou `fill` com um container de tamanho definido.
- Com `fill`, sempre informe `sizes`, para o navegador baixar o tamanho certo:

```tsx
<div className="relative aspect-square">
  <Image
    src={product.images[0]}
    alt={`Foto de ${product.name}`}
    fill
    sizes="(max-width: 768px) 50vw, 25vw"
    className="object-cover"
  />
</div>
```

- A imagem principal acima da dobra (o maior elemento visível ao abrir a página, que conta para o LCP) é carregada com prioridade. Historicamente isso era feito com a prop `priority`. Confira na documentação da versão instalada, porque versões mais novas do Next mudaram essa API.
- Domínios de imagens remotas precisam ser liberados em `images.remotePatterns` no `next.config` (ver [Configuração do Next.js](12-next-config.md)).

## React + Vite

Sem `next/image`, as otimizações são manuais:

```tsx
<img
  src={photo.url}
  srcSet={`${photo.small} 400w, ${photo.large} 800w`}
  sizes="(max-width: 768px) 50vw, 25vw"
  width={400}
  height={400}
  loading="lazy"
  decoding="async"
  alt={`Foto de ${product.name}`}
/>
```

- Sempre `width` e `height` (ou `aspect-ratio` no CSS), para evitar que a página "pule" quando a imagem carrega (CLS).
- `loading="lazy"` em tudo que está fora da primeira tela. Na imagem principal, `fetchpriority="high"` e sem lazy.
- Importe imagens de `src/assets/` pelo código, para o Vite gerar o hash no nome.

## Texto alternativo (`alt`)

- Descreve o que a imagem mostra e importa para o conteúdo: `"Tênis branco de corrida visto de lado"`, não `"imagem"` ou `"foto"`.
- Imagens puramente decorativas usam `alt=""`, para o leitor de tela ignorar.
- Imagem que funciona como link ou botão descreve a ação.

## Ícones

- **lucide-react**, que é o padrão do shadcn. Importe cada ícone individualmente: `import { Heart } from "lucide-react"`.
- Ícones apenas decorativos levam `aria-hidden="true"`.
- Botão com apenas ícone precisa de `aria-label`: `<button aria-label="Fechar">`.

## Fontes

- **Next.js:** `next/font`. A fonte é hospedada junto com o app, sem requisição ao Google Fonts, e sem "pulo" de layout ao carregar.
- **React + Vite:** pacotes do **Fontsource** (`@fontsource-variable/inter`).
- No máximo duas famílias. Prefira fontes variáveis ou carregue só os pesos usados.

## PWA e favicon

- Ícones do manifest em `public/icons/`, nos tamanhos 192×192 e 512×512, mais uma versão **maskable** (com margem de segurança, para o Android recortar em círculo ou outros formatos).
- `apple-touch-icon` 180×180 para iOS.
- Favicon em SVG (com fallback `.ico`).

## Imagens enviadas por usuários

Quando o app receber uploads, as imagens devem ser redimensionadas e convertidas no armazenamento ou no servidor (por exemplo, com as transformações de imagem do provedor de storage), nunca servidas no tamanho original.

## Verificação

Rode o Lighthouse antes de cada entrega. Os avisos "Properly size images", "Serve images in next-gen formats" e "Largest Contentful Paint" apontam exatamente o que corrigir.
