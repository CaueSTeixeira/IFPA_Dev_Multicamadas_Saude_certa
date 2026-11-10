# ADR-003: Sessão com cookie e RBAC em duas etapas (perfil e vínculo)

**Em uma frase:** login com sessão e controle de acesso em duas etapas: primeiro o perfil, depois o vínculo com o paciente.

**Status:** aceita
**Drivers:** RNF03, RNF08, RN07, RN10, RN12, RF11

## Contexto

É preciso autenticar quatro perfis e garantir que o profissional só leia pacientes com quem tem vínculo e que o paciente só leia os próprios dados. Há um único servidor.

## Decisão

Spring Security com login por usuário e senha (BCrypt) e sessão mantida no servidor, com cookie `HttpOnly` e proteção CSRF. A autorização acontece em duas etapas: (1) por perfil, com `@PreAuthorize` nos controllers; (2) por vínculo profissional-paciente (RN10) ou propriedade do paciente (RN12), na camada de aplicação.

## Alternativas consideradas

| Alternativa | Vantagem | Desvantagem |
|---|---|---|
| JWT sem estado | Pronto para vários serviços | Revogação difícil; mais código e cuidado com o token |
| OAuth2 ou Keycloak | Robusto e padronizado | Servidor extra e curva de aprendizado; fora da stack do guia |
| Só perfil, sem vínculo | Mais simples | Profissional acessaria qualquer paciente, violando RN07 |

## Consequências

- (+) Desativar um usuário tem efeito imediato.
- (+) Usa só o Spring Security.
- (−) A sessão fica em memória e se perde ao reiniciar (o usuário entra de novo).
- (−) Ao extrair um serviço no Projeto Final será preciso reavaliar o uso de JWT.

## Custo/benefício

Custo baixo; mantém a recepção ágil (RNF07) com poucos perfis e protege o dado clínico no ponto certo.
