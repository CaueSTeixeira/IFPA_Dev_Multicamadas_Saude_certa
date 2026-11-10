# ADR-001: Arquitetura multicamadas em monólito modular

**Em uma frase:** um monólito dividido em camadas e módulos por domínio, em vez de microsserviços.

**Status:** aceita
**Drivers:** evolução e simplicidade (RNF01, RNF02)

## Contexto

Equipe de 3 a 4 pessoas, clínica de cerca de 20 profissionais, servidor local único. Microsserviços trariam deploy, rede e observabilidade sem benefício imediato, e o sistema precisa crescer por módulos sem reescrever o núcleo.

## Decisão

Três tiers (cliente, aplicação, dados); a aplicação é um monólito em quatro camadas e em módulos por domínio (cadastro, agenda, prontuário, prescrição, relatórios, segurança), com a mesma estrutura de pacotes em todos.

## Alternativas consideradas

| Alternativa | Vantagem | Desvantagem |
|---|---|---|
| Microsserviços desde o início | Implantação e escala independentes | Complexidade operacional sem benefício nesta escala |
| Duas camadas (tela acessa o banco) | Muito simples | Sem controle de acesso e auditoria centralizados; fere segurança |
| Hexagonal completa | Isolamento máximo | Excesso de interfaces para o porte do projeto |
| Monólito em camadas sem módulos (pacotes por camada) | Familiar | Cada mudança se espalha por todas as camadas; extração futura difícil |

## Consequências

- (+) Entrega rápida e operação simples.
- (+) Fronteiras prontas para extrair um serviço no Projeto Final.
- (−) Exige disciplina para não acoplar módulos, verificada em revisão de código.

## Custo/benefício

Custo baixo (um deploy, um banco); o benefício principal é a modificabilidade sem o preço de serviços distribuídos.
