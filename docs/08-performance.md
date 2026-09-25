# 🚄 Performance

## Estado e renderização

- Estado perto de onde é usado. Estado global só quando necessário.
- Divida o estado global em stores menores por assunto.
- **Composição com `children`:** o JSX passado como `children` não re-renderiza quando o estado do pai muda.
- Inicialização cara com função: `useState(() => computeInitialValue())`.

## Carregamento

- Code splitting por rota (automático no Next; com `lazy()` no React + Vite).
- Não exagerar no code splitting: muitos chunks pequenos também custam.
- **Prefetch** com `queryClient.prefetchQuery` quando a próxima navegação é previsível.

## Imagens e fontes

Formatos, tamanhos, `next/image`, lazy loading, ícones e fontes estão detalhados em [Assets e imagens](11-assets-and-images.md).

## Estilo

Soluções sem runtime (Tailwind, CSS Modules), que também funcionam com Server Components.

## Medição

Acompanhar os Core Web Vitals com Lighthouse e PageSpeed Insights.
