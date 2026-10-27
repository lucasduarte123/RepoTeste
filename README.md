# Ecommerce API Tests

Testes automatizados de API para o fluxo de compra de um e-commerce, do login ao checkout, escritos no Postman e executados pelo Newman no GitHub Actions.

A API usada é a [DummyJSON](https://dummyjson.com), uma API pública de testes com usuários, produtos e carrinhos.

![Testes de API](../../actions/workflows/api-tests.yml/badge.svg)

## Resultado da última execução local

| Requisições | Asserções | Falhas | Tempo médio de resposta |
|---|---|---|---|
| 4 | 10 | 0 | 192 ms |

Relatório completo: [`reports/exemplo-2026-09-25.html`](reports/exemplo-2026-09-25.html) (baixe e abra no navegador).

## O que é testado

O fluxo segue a ordem de uma compra real. O token gerado no login é salvo no ambiente e reaproveitado nas requisições seguintes.

| # | Requisição | Método e rota | Verificações |
|---|---|---|---|
| 1 | Login | `POST /auth/login` | Status 200 · resposta em menos de 1,5 s · `accessToken` presente · `id` é número e `username` é texto · salva o token |
| 2 | Catálogo de produtos | `GET /products` | Status 200 · resposta em menos de 1,5 s · `products` é uma lista |
| 3 | Adicionar ao carrinho | `POST /carts/add` | Status 201 · carrinho criado com `id` · produto 1 no carrinho · quantidade total igual a 2 |
| 4 | Checkout | `GET /carts/user/1` | Status 200 · carrinho com `total` e `discountedTotal` |

Tipos de teste cobertos: **status code**, **tempo de resposta (SLA)**, **contrato/schema** da resposta, **regra de negócio** (quantidade no carrinho) e **encadeamento** de requisições com variável de ambiente.

## Como rodar

### No Postman
1. Importe `collections/ecommerce-api-tests.postman_collection.json`.
2. Importe `environments/dev.postman_environment.json` e selecione o ambiente **Ecommerce - Dev**.
3. Clique em **Run collection**.

### Pela linha de comando (Newman)
```bash
npm install -g newman newman-reporter-htmlextra
newman run collections/ecommerce-api-tests.postman_collection.json \
  -e environments/dev.postman_environment.json \
  -r cli,htmlextra --reporter-htmlextra-export reports/relatorio.html
```

### No GitHub Actions
O workflow [`api-tests.yml`](.github/workflows/api-tests.yml) roda a coleção a cada push, todos os dias às 9h e quando acionado manualmente em **Actions > Testes de API > Run workflow**. O relatório HTML fica salvo como artefato da execução.

## Estrutura

```
collections/   Coleção do Postman com as requisições e os scripts de teste
environments/  Variáveis do ambiente (baseUrl e token)
reports/       Exemplo de relatório gerado pelo Newman
.github/       Pipeline de CI que executa os testes
```

## Próximos passos

- Cenários negativos: login com senha errada (401), produto inexistente (404), carrinho com quantidade zero
- Validação de schema completa com JSON Schema (`pm.response.to.have.jsonSchema`)
- Massa de dados com arquivo CSV para rodar o mesmo teste com vários usuários

---

Feito por [Lucas Duarte](https://www.linkedin.com/in/lucas-duarte-59b95b67) · Analista de QA · Florianópolis/SC
