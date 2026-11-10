# ADR-006: Cliente web em React e banco PostgreSQL (H2 em desenvolvimento)

**Em uma frase:** o cliente é uma aplicação React separada da API, e o banco é PostgreSQL em produção, com H2 só no desenvolvimento.

**Status:** aceita
**Drivers:** RNF07, RNF02, RNF01; E1

## Contexto

A E1 define um cliente web separado, sem regra de negócio, que consome a API REST, e PostgreSQL em produção. O guia lista H2 para desenvolvimento, PostgreSQL como opcional e Postman para testar a API.

## Decisão

O cliente é uma SPA em React + TypeScript (Vite), servida como arquivos estáticos, que fala só com a API. O banco é H2 em desenvolvimento e testes e PostgreSQL em produção, trocado por configuração, sem SQL específico de banco. Postman ou Insomnia continuam sendo usados para testar a API.

## Alternativas consideradas

| Alternativa | Vantagem | Desvantagem |
|---|---|---|
| Só H2, em modo arquivo | Nada para instalar | Menos robusto com acesso concorrente; roda dentro da API, então o tier de dados não fica separado |
| Páginas Thymeleaf, sem SPA | Um só projeto | Mistura apresentação com o servidor e foge do cliente separado da E1 |
| Angular no lugar de React | Estrutura mais rígida | Curva de aprendizado maior |
| Sem front-end, só Postman | Menos trabalho | Não permite medir a usabilidade da recepção (RNF07) |

## Consequências

- (+) O cliente é separado e a mesma API serve interfaces futuras (mobile, portal do paciente).
- (+) PostgreSQL lida bem com acessos simultâneos e tem backup simples com `pg_dump`.
- (+) A usabilidade da recepção pode ser medida (RNF07).
- (−) Exige conhecer JavaScript, TypeScript, CORS e CSRF.
- (−) Desenvolver em H2 e rodar em PostgreSQL pode esconder diferenças de SQL, então os testes de integração finais devem rodar também contra PostgreSQL.

## Custo/benefício

Custo moderado (outra linguagem e um banco para instalar); o benefício é um cliente separado, como a E1 pede, e um banco adequado ao uso real da clínica.
