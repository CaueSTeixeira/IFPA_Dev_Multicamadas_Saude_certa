# ADR-004: Conflito de horário com regra no domínio e bloqueio pessimista por profissional

**Em uma frase:** a regra de conflito fica no domínio, e um bloqueio por profissional evita marcação dupla simultânea.

**Status:** aceita
**Drivers:** RF07, RN02, RNF07

## Contexto

A checagem de sobreposição, se feita só na aplicação, falha quando duas recepcionistas marcam o mesmo horário ao mesmo tempo: ambas leem "livre" e ambas gravam. Com 20 profissionais e várias recepcionistas isso é plausível.

## Decisão

A regra (início menor que o fim existente e fim maior que o início existente, só entre consultas `AGENDADAS`) fica no domínio, em `ConflitoHorarioPolicy`. O `AgendaService` executa a marcação e a remarcação em uma transação que antes bloqueia a linha do profissional (`@Lock PESSIMISTIC_WRITE`), verifica e grava. O conflito retorna HTTP 409.

## Alternativas consideradas

| Alternativa | Vantagem | Desvantagem |
|---|---|---|
| Só checagem na aplicação | Mais simples | Condição de corrida |
| Constraint de exclusão no banco | Garantia no próprio banco | Recurso só do PostgreSQL; o H2 do desenvolvimento não tem, e o teste perderia a paridade |
| Lock otimista (versão) | Sem bloqueio | Não protege a inserção de novas linhas |

## Consequências

- (+) Garantia real contra dupla marcação no H2 e no PostgreSQL.
- (+) Regra testável sem banco.
- (−) Marcações do mesmo profissional são serializadas, o que não pesa nesta escala.

## Custo/benefício

Custo baixo (uma anotação e um teste concorrente); evita o defeito de negócio mais visível do sistema.
