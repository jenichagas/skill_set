# 🔐 Segurança

A segurança real está no servidor. Verificações no front servem para a experiência do usuário (esconder botões, redirecionar), nunca como proteção.

## Autenticação

- Sessão em **cookie httpOnly**, inacessível ao JavaScript do navegador.
- Nunca guardar token em `localStorage`, por causa do risco de XSS.
- No Next, a proteção de rotas fica centralizada no `proxy.ts` (antes `middleware.ts`).
- Usuário desativado perde o acesso na próxima requisição, não só no próximo login.

## Autorização

**RBAC (por perfil):** acesso definido pelo papel do usuário (`ADMIN`, `USER`).

**PBAC (por política):** quando a permissão depende do recurso, como "só o autor pode editar". As políticas são funções puras em `src/lib/authorization.ts`, usadas no front e no servidor:

```ts
export const canEditPost = (user: User, post: Post) => post.authorId === user.id;
```

## Entrada do usuário

- Toda entrada é validada com **Zod no servidor**, com o mesmo schema usado no formulário.
- Nunca renderizar HTML vindo do usuário sem sanitizar. Evite `dangerouslySetInnerHTML`.
- Headers de segurança e variáveis de ambiente: ver [Configuração do Next.js](12-next-config.md).
- Referência: [OWASP Top 10](https://owasp.org/www-project-top-ten/).
