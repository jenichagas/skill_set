# ⚠️ Tratamento de erros

## Erros de API

Tratados de forma central no `api-client`: toast com mensagem amigável e logout automático em `401`.

## Erros na interface

- **Error boundaries por área**, não um único para o app inteiro. No Next, um `error.tsx` em cada segmento de rota relevante.
- `not-found.tsx` para recursos inexistentes ou sem permissão.
- Mensagens de erro para o usuário em português e sem detalhes técnicos.

## Logs

- `console.error` e `console.warn` só para falhas reais, no servidor.
- `console.log` é bloqueado pelo lint.

## Produção

Rastreamento com Sentry, enviando source maps, quando o projeto for para produção de verdade.
