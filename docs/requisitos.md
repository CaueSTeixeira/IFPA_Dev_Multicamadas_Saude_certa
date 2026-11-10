# SaúdeCerta — Requisitos

## Entrega 1: Requisitos funcionais, regras de negócio, requisitos não funcionais e casos de uso


---

## 3. Requisitos funcionais (RF)

| ID | Requisito | Critério de aceitação |
|---|---|---|
| RF01 | Cadastrar e manter pacientes. | Ao informar nome completo, CPF, telefone, data de nascimento e convênio válidos, o sistema salva, gera o código único do paciente e passa a listá-lo na busca (por nome ou código). Sem qualquer um desses campos, recusa. |
| RF02 | Cadastrar profissionais e especialidades. | Um profissional tem uma ou mais especialidades; não é possível salvar sem ao menos uma. |
| RF03 | Manter tipos de consulta com duração. | Existem ao menos os tipos "primeira consulta" (40 min) e "retorno" (20 min), cada um com duração própria configurável. |
| RF04 | Definir a disponibilidade do profissional. | O `ADMIN` define dias e faixas de horário; o sistema gera os horários marcáveis conforme a duração do tipo de consulta. O médico apenas consulta a própria agenda. |
| RF05 | Marcar consulta. | Ao escolher paciente, profissional, tipo, data e horário livres, a consulta é criada com status `AGENDADA`. |
| RF06 | Remarcar e cancelar consulta. | Consulta `AGENDADA`, com antecedência mínima de 24h, pode ser remarcada ou cancelada; o horário anterior volta a ficar livre. |
| RF07 | Impedir conflito de horário. | O sistema recusa marcação que se sobreponha a outra consulta `AGENDADA` do mesmo profissional (RN02). |
| RF08 | Registrar atendimento no prontuário. | O médico registra queixa, evolução e conduta, vinculadas ao paciente e à consulta; ao finalizar, o registro fica bloqueado. |
| RF09 | Registrar adendo. | Correções em atendimento finalizado entram como adendo, com autor e data/hora, sem alterar o registro original. |
| RF10 | Consultar histórico do paciente. | O usuário clínico autorizado (`MEDICO` com vínculo, ou `ADMIN`) vê, em ordem cronológica, atendimentos, adendos, prescrições e exames do paciente. |
| RF11 | Autenticar usuários e aplicar perfis. | O acesso a cada função depende do perfil (`RECEPCAO`, `MEDICO`, `PACIENTE`, `ADMIN`). |
| RF12 | Registrar auditoria de acesso ao prontuário. | Todo acesso, alteração ou tentativa negada grava quem, quando, qual paciente e qual ação. |
| RF13 | Gerenciar usuários. | O `ADMIN` cria, desativa e atribui perfil a usuários. |
| RF14 | Emitir prescrição. | Uma prescrição tem um ou mais itens (medicamento, dose, posologia) e fica vinculada a um atendimento em andamento. |
| RF15 | Solicitar exames. | O atendimento pode gerar uma ou mais solicitações de exame, com status de acompanhamento (`SOLICITADO` ou `REALIZADO`). |
| RF16 | Gerar relatórios. | O `ADMIN` obtém consultas por período, por profissional e por especialidade, com totalizadores. |
| RF17 | Paciente consulta os próprios dados. | O paciente autenticado vê seus dados, seus agendamentos e seu prontuário, somente leitura; nunca os de outro paciente. |

---

## 4. Regras de negócio (RN)

| ID | Regra |
|---|---|
| RN01 | O paciente é identificado por um **código único gerado pelo sistema**, imutável. O CPF é dado cadastral obrigatório e validado, mas **não é chave de identificação**. Antes de salvar, o sistema alerta possível duplicidade (mesmo CPF ou mesmo nome e data de nascimento). |
| RN02 | Um profissional não pode ter duas consultas `AGENDADAS` com horários sobrepostos (considerando a duração de cada uma). |
| RN03 | A duração da consulta é definida pelo tipo (primeira consulta ou retorno). |
| RN04 | Só é possível registrar atendimento para consultas cuja data já ocorreu ou é a do dia. |
| RN05 | Remarcar ou cancelar exige consulta com status `AGENDADA` e antecedência mínima de 24h (parâmetro configurável). |
| RN06 | O prontuário é somente-anexação: o atendimento finalizado fica bloqueado e correções entram apenas como adendo. |
| RN07 | O conteúdo clínico só é acessível ao médico que atende o paciente (RN10), ao `ADMIN` e ao próprio paciente (somente leitura). A recepção nunca acessa conteúdo clínico. |
| RN08 | Todo acesso ao prontuário (inclusive do administrador, do paciente e tentativas negadas) é registrado na auditoria. |
| RN09 | O cadastro do paciente exige nome completo, CPF, telefone, data de nascimento e convênio ("Particular" é um valor válido). |
| RN10 | Um médico tem vínculo com o paciente se existir ao menos uma consulta dele com esse paciente em status `AGENDADA` ou `REALIZADA`. Sem vínculo, o acesso ao prontuário é negado e auditado. O `ADMIN` não precisa de vínculo. |
| RN11 | Prescrição e solicitação de exame só existem vinculadas a um atendimento em andamento e, como parte do prontuário, não são apagadas (RN06). |
| RN12 | O paciente só enxerga os próprios dados e histórico. |

---

## 5. Requisitos não funcionais (RNF) e cenários de qualidade

| ID | Atributo | Cenário (fonte, estímulo, resposta, medida) |
|---|---|---|
| RNF01 | Modificabilidade | Equipe inclui um módulo novo (ex.: faturamento) sem alterar o núcleo de agenda e prontuário; esforço estimado de até 5 dias por equipe. |
| RNF02 | Manutenibilidade e simplicidade | Um novo integrante clona o repositório e sobe o sistema local em até 30 min seguindo o README; todo módulo segue a mesma estrutura de pacotes. |
| RNF03 | Segurança e privacidade (LGPD) | Usuário sem perfil adequado, ou médico sem vínculo, tenta abrir um prontuário; o sistema nega e registra a tentativa; 100% das tentativas indevidas bloqueadas. |
| RNF04 | Auditabilidade | Usuário abre um prontuário; o sistema registra usuário, paciente, data/hora e ação; o registro fica disponível ao admin em até 1 min. |
| RNF05 | Desempenho | Usuário busca paciente por nome ou abre prontuário, com carga de referência de 20 profissionais, 10 mil pacientes e 50 mil consultas; resultado em menos de 2 s no percentil 95. |
| RNF06 | Disponibilidade | O sistema roda em servidor único na clínica, com meta de 99% de disponibilidade ao mês no horário de funcionamento, reinício automático e healthcheck (Actuator). |
| RNF07 | Usabilidade | Recepcionista marca uma consulta simples em até 1 min e 5 cliques a partir da tela inicial. |
| RNF08 | Segurança de credenciais | Senhas nunca armazenadas em texto; hash com sal (BCrypt), verificado em revisão de código. |
| RNF09 | Recuperabilidade | Ocorre perda do disco do servidor; o banco é restaurado a partir do backup diário em outro dispositivo; perda máxima de 24 h de dados; restauração testada mensalmente. |

---

## 6. Casos de uso principais

### UC01: Marcar consulta (ator: Recepcionista)

1. A recepcionista busca o paciente ou, se for novo, cadastra (RF01). O sistema alerta possível duplicidade (RN01).
2. Seleciona a especialidade, o profissional e o tipo de consulta.
3. O sistema mostra os horários livres da agenda (RF04), conforme a duração do tipo.
4. Escolhe data e horário e confirma.
5. O sistema valida o conflito por bloqueio pessimista (RN02, RF07) e cria a consulta como `AGENDADA` (RF05).

**Fluxo alternativo:** horário ocupado, o sistema informa na hora e volta ao passo 3.

### UC02: Atender paciente (ator: Médico)

1. O médico abre a agenda do dia e seleciona a consulta.
2. O sistema verifica o perfil e o vínculo com o paciente (RN07, RN10), registra o acesso na auditoria (RF12) e mostra o histórico (RF10).
3. O médico registra queixa, evolução e conduta (RF08), além de emitir prescrições (RF14) e solicitar exames (RF15).
4. Ao finalizar, o atendimento fica bloqueado (RN06) e a consulta passa a `REALIZADA`.
5. Correções posteriores entram como adendo (RF09).

**Fluxo alternativo:** sem vínculo ou sem perfil clínico, o sistema nega o acesso, registra a tentativa e não exibe conteúdo clínico (RNF03).

### UC03: Remarcar ou cancelar consulta (ator: Recepcionista)

1. A recepcionista localiza a consulta do paciente.
2. O sistema confirma que ela está `AGENDADA` e com antecedência mínima de 24 h (RN05).
3. Para remarcar, escolhe novo horário livre e o sistema valida o conflito; para cancelar, confirma o cancelamento.
4. O horário anterior volta a ficar livre (RF06).

**Fluxo alternativo:** consulta fora de `AGENDADA` ou com menos de 24 h, o sistema recusa e informa o motivo.
