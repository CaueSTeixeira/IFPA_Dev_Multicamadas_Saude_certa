# ADR-002: Auditoria de acesso ao prontuário assíncrona, por eventos

**Em uma frase:** o acesso ao prontuário gera um evento, e o registro de auditoria é gravado em segundo plano.

**Status:** aceita
**Drivers:** RNF04, RNF05, RNF03; RF12, RN08

## Contexto

Todo acesso ao prontuário deve ser registrado (RNF04), mas a abertura precisa responder em menos de 2 s (RNF05). Gravar a auditoria na mesma transação de cada leitura soma uma escrita ao caminho crítico.

## Decisão

Publicar o evento de domínio `ProntuarioAcessado` (usuário, paciente, ação `LEITURA` ou `ALTERACAO`, resultado `PERMITIDO` ou `NEGADO`, data/hora) a cada acesso, inclusive os negados, e processá-lo de forma assíncrona (`@Async`) no `AuditoriaListener`, fora do caminho crítico. Em caso de falha, o listener tenta 3 vezes e, se persistir, registra o evento em arquivo de log para reconciliação.

## Alternativas consideradas

| Alternativa | Vantagem | Desvantagem |
|---|---|---|
| Gravar na mesma transação da leitura (síncrona) | Simples; nenhum registro perdido | Penaliza o desempenho de leitura (RNF05) |
| Fila externa (RabbitMQ ou Kafka) | Entrega garantida e desacoplamento | Novo componente a operar; fere a simplicidade e a stack do guia |
| Outbox transacional | Sem perda de evento | Mais código e tabela extra; reavaliar se o sistema crescer |

## Consequências

- (+) Preserva o desempenho de leitura.
- (+) Desacopla a auditoria dos casos de uso.
- (−) Risco de pequena defasagem ou, após queda do processo, de perder um registro recente.
- (−) Exige tratar falhas do consumidor.

## Custo/benefício

Custo baixo (um listener e um pool de execução, sem biblioteca nova); benefício alto, pois atende RNF04 e RNF05 ao mesmo tempo.
