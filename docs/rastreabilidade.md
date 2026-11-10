# SaúdeCerta — Matriz de rastreabilidade

## Entrega 2: requisitos ↔ arquitetura

A matriz liga cada requisito a uma decisão ([ADR](adr/)), ao componente ou tática que o atende e a uma forma de verificação. Detalhes dos componentes em [`arquitetura.md`](arquitetura.md).

| Requisito | Decisão | Componente ou tática | Como verificar |
|---|---|---|---|
| RF01, RN01, RN09 (pacientes) | [ADR-005](adr/ADR-005-dominio-isolado-da-persistencia.md) | `Paciente`, `PacienteService`, Bean Validation, alerta de duplicidade | Cadastro sem campo obrigatório é recusado; CPF repetido gera alerta |
| RF02, RF03, RF04 (profissionais, tipos, disponibilidade) | [ADR-001](adr/ADR-001-arquitetura-multicamadas.md) | Módulo cadastro; `Disponibilidade`; `CalculadoraHorariosLivres` | Profissional sem especialidade é recusado; horários respeitam a duração |
| RF05, RF07, RN02, RN03 (marcar sem conflito) | [ADR-004](adr/ADR-004-conflito-de-horario.md) | `ConflitoHorarioPolicy`; `AgendaService` com lock | Teste unitário de sobreposição; teste concorrente com duas marcações |
| RF06, RN05 (remarcar e cancelar) | [ADR-005](adr/ADR-005-dominio-isolado-da-persistencia.md) | `Consulta.remarcar()` e `cancelar()` | Cancelar com menos de 24 h ou fora de `AGENDADA` é recusado |
| RF08, RF09, RN04, RN06 (atendimento e adendo) | [ADR-005](adr/ADR-005-dominio-isolado-da-persistencia.md) | `Atendimento.finalizar()`, `Adendo`, repositório sem delete | Editar atendimento finalizado é recusado; adendo preserva o original |
| RF14, RF15, RN11 (prescrição e exames) | [ADR-001](adr/ADR-001-arquitetura-multicamadas.md), [ADR-005](adr/ADR-005-dominio-isolado-da-persistencia.md) | Módulo prescricao; `AtendimentoQuery` | Prescrição sem atendimento em andamento é recusada |
| RF10, RN07, RN10, RNF03 (acesso clínico) | [ADR-003](adr/ADR-003-sessao-e-rbac.md) | `@PreAuthorize` e `VinculoProfissionalPacientePolicy` | Acesso negado para recepção e para profissional sem vínculo |
| RF17, RN12 (paciente) | [ADR-003](adr/ADR-003-sessao-e-rbac.md) | Perfil `PACIENTE` e regra de propriedade | Paciente lê o próprio prontuário e recebe 403 no de outro |
| RF11, RF13 (perfis e usuários) | [ADR-003](adr/ADR-003-sessao-e-rbac.md) | Spring Security, `UsuarioService` | Testes por perfil em cada endpoint |
| RF12, RN08, RNF04 (auditoria) | [ADR-002](adr/ADR-002-auditoria-assincrona.md) | `ProntuarioAcessado`, `AuditoriaListener` | Registro existe após abrir prontuário; negações também são registradas |
| RF16 (relatórios) | [ADR-001](adr/ADR-001-arquitetura-multicamadas.md) | Módulo relatorios; `ConsultasAgregadasQuery` | Totais conferem com dados de teste; só `ADMIN` acessa |
| RNF01 (modificabilidade) | [ADR-001](adr/ADR-001-arquitetura-multicamadas.md), [ADR-005](adr/ADR-005-dominio-isolado-da-persistencia.md) | Módulos por domínio, portas, eventos | Revisão de código: sem ciclos nem acesso ao repositório de outro módulo |
| RNF02 (manutenibilidade) | [ADR-001](adr/ADR-001-arquitetura-multicamadas.md), [ADR-006](adr/ADR-006-react-e-postgresql.md) | Estrutura de pacotes única, H2, README | Colega novo sobe o sistema em até 30 min |
| RNF05 (desempenho) | [ADR-002](adr/ADR-002-auditoria-assincrona.md) | Índices, paginação, auditoria assíncrona | Medir o p95 com a carga de teste (20 profissionais, 10 mil pacientes, 50 mil consultas) |
| RNF06 (disponibilidade) | [ADR-001](adr/ADR-001-arquitetura-multicamadas.md) | Reinício automático, Spring Actuator, transações curtas | Matar o processo e medir o retorno; consultar `/actuator/health` |
| RNF07 (usabilidade) | [ADR-006](adr/ADR-006-react-e-postgresql.md), [ADR-004](adr/ADR-004-conflito-de-horario.md) | SPA com fluxo UC01 curto; erro 409 claro | Cronometrar e contar cliques no fluxo de marcação |
| RNF08 (credenciais) | [ADR-003](adr/ADR-003-sessao-e-rbac.md) | BCrypt no `UsuarioService` | Revisão de código; checar a coluna `senhaHash` |
| RNF09 (recuperabilidade) | [ADR-006](adr/ADR-006-react-e-postgresql.md) | Script `pg_dump` agendado, com cópia | Restaurar o backup em outro ambiente uma vez por mês |
