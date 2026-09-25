# 🧪 Testes

## Prioridade

1. **Integração:** telas e fluxos testados como o usuário usa. É onde está a maior parte do valor.
2. **Unitários:** funções puras com regra de negócio (políticas de autorização, transições de status, utils).
3. **E2E:** fluxos críticos de ponta a ponta, quando o projeto amadurecer.

## Ferramentas

- **Vitest** como test runner.
- **Testing Library**: testar o que aparece na tela, não o estado interno. Se trocar a biblioteca de estado, os testes continuam válidos.
- **MSW** para simular a API nos testes, com handlers em `src/testing/mocks/`. Não mockar `fetch` diretamente.
- **Playwright** para E2E: modo com navegador localmente, modo headless no CI.

## Onde ficam

Ao lado do código testado, em `__tests__/` ou como `nome.test.ts(x)`.
