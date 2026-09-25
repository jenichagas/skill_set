# 🗃️ Gerenciamento de estado

Cada tipo de estado tem um lugar:

| Tipo | Exemplo | Onde |
|---|---|---|
| Componente | modal aberto, input controlado | `useState` / `useReducer` |
| Aplicação (muda pouco) | sessão, tema | React Context |
| Aplicação (muda muito) | filtros, carrinho, etapas de formulário | Zustand, com seletores |
| Servidor | dados da API | TanStack Query |
| Formulário | campos e validação | React Hook Form + Zod |
| URL | busca, filtros compartilháveis, paginação | query params |

## Regras

- **Comece local.** Suba o estado só quando outro componente precisar.
- **Dado da API não vai para store.** O Zustand guarda só estado criado no cliente.
- **Context só para dados que mudam pouco.** Estado que muda com frequência em Context causa re-render em cascata.
- **Use seletores no Zustand:** `useFiltersStore((s) => s.filters.category)`, nunca a store inteira.
- **Filtros de tabela vão para a URL,** para sobreviverem a recarregar, voltar e compartilhar o link.
- **Update otimista** em ações rápidas do usuário (curtir, marcar como concluído).
