# 📡 Camada de API

## Um único cliente

`src/lib/api-client.ts` é a única instância usada pelos fetchers. Ele centraliza URL base, headers, conversão de erros e o tratamento global (toast de erro, logout em `401`).

## Um arquivo por requisição

Cada arquivo em `features/*/api/` contém tudo sobre uma chamada:

1. Tipos e schemas da entrada e da resposta
2. A função fetcher, usando o `api-client`
3. As query options e o hook do TanStack Query

```ts
// features/products/api/get-products.ts
import { queryOptions, useQuery } from "@tanstack/react-query";
import { api } from "@/lib/api-client";
import type { Product, ProductFilters } from "@/types/product";

export const getProducts = (filters: ProductFilters): Promise<Product[]> =>
  api.get("/products", { params: filters });

export const getProductsQueryOptions = (filters: ProductFilters) =>
  queryOptions({
    queryKey: ["products", filters],
    queryFn: () => getProducts(filters),
  });

export const useProducts = (filters: ProductFilters) =>
  useQuery(getProductsQueryOptions(filters));
```

Mutations seguem o mesmo padrão (`create-product.ts`, `update-product.ts`) e invalidam as queries afetadas no `onSuccess`.

As query options exportadas permitem reutilizar a mesma query em prefetch e em Server Components.

## Requisições compartilhadas

Se mais de uma feature precisa da mesma requisição, ela vai para `src/lib/api/`.
