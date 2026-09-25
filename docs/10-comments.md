# 💬 Comentários

O padrão é **não comentar**. Nomes claros tornam o código autoexplicativo.

## Quando comentar

Só quando o código não consegue explicar sozinho **o porquê**:

- Uma regra de negócio não óbvia
- O contorno de um bug ou limitação de biblioteca
- Uma decisão que parece errada, mas é intencional

## Como

- Uma linha, no máximo duas.
- Explica o porquê, nunca o quê.
- Sem parágrafos nem explicações de conceitos.

```ts
// ✅
// O gateway de pagamento pode reenviar o mesmo webhook; eventos repetidos são ignorados.
if (await isEventProcessed(event.id)) return;

// ❌ repete o código
// Busca os produtos
const products = await getProducts();
```

## Nunca

- Comentários HTML (`<!-- -->`) em nada renderizado. Só `//` e `{/* */}`, que o build remove.
- Código comentado. O Git guarda o histórico.
- `console.log` de debug.
- Marcadores óbvios (`// imports`, `// handlers`).
- JSDoc em código interno. Os tipos já documentam.
