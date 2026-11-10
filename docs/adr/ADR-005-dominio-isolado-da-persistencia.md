# ADR-005: Domínio isolado da persistência, com mappers manuais

**Em uma frase:** o domínio é Java puro; entidades JPA e mappers manuais ficam na camada de persistência.

**Status:** aceita
**Drivers:** RNF01, RNF02; RN02, RN06

## Contexto

A E1 define que o domínio não conhece JPA, HTTP nem Spring Security, e o guia lista "vazar JPA no domínio" como erro comum.

## Decisão

O domínio contém classes Java puras, com comportamento (entidades ricas, sem setters públicos). Na infra existem entidades JPA próprias e mappers escritos à mão, sem biblioteca de mapeamento. Os repositórios são interfaces do domínio implementadas por adaptadores. Atendimento, Adendo, Prescrição e Solicitação de exame não têm operação de exclusão (RN06, RN11).

## Alternativas consideradas

| Alternativa | Vantagem | Desvantagem |
|---|---|---|
| Anotações JPA direto no domínio | Menos classes | Domínio acoplado ao framework; testes exigem contexto JPA |
| Biblioteca de mapeamento (MapStruct) | Menos código repetitivo | Dependência fora da stack do guia |
| Hexagonal completa | Isolamento total | Excesso de abstração |

## Consequências

- (+) Regras RN02, RN05 e RN06 testadas em milissegundos, sem Spring nem banco.
- (+) Fronteira limpa para extração de serviço.
- (−) Uma classe de entidade e um mapper a mais por agregado.

## Custo/benefício

Custo moderado em código repetitivo, compensado pelos testes rápidos exigidos na E3 e pela facilidade de evolução.
