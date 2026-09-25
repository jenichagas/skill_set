# 🧱 Componentes e estilo

## Boas práticas

- **Colocation:** componentes, estado e estilos ficam o mais perto possível de onde são usados.
- **Sem funções de renderização internas.** Em vez de `renderItems()`, extraia um componente `<Items />`.
- **Poucas props.** Um componente com props demais deve ser dividido ou usar composição via `children`.
- **Abstraia só depois de ver a repetição.** Abstração prematura custa mais do que duplicação.
- **Envolva componentes de terceiros** quando precisar adaptá-los, para trocar a biblioteca sem afetar o resto do app.

## Biblioteca de componentes

- **shadcn/ui** como base: os componentes ficam no código do projeto, em `components/ui/`, e podem ser customizados.
- Por baixo, primitivos headless (Radix ou Base UI), que já resolvem acessibilidade.

## Estilo

- **Tailwind CSS.** Não gera estilo em runtime, então funciona com Server Components.
- Classes condicionais com o utilitário `cn()` do shadcn.

## Formulários

Componentes base em `components/form/` (`Form`, `FormField`, `Input`, `Select`, `Textarea`), já integrados com React Hook Form, label, mensagem de erro e atributos de acessibilidade. Os formulários das features usam esses componentes em vez de repetir essa estrutura.

## Acessibilidade

- HTML semântico.
- `label` em todo input.
- Foco visível e navegação por teclado.
- Mobile first.

## Estados de tela

Toda tela que busca dados trata **loading, erro e vazio**.
