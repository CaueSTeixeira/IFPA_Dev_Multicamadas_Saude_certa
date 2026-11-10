# SaúdeCerta — Sistema de Gestão de Clínica

## Entrega 1: Visão do produto

IFPA · Tecnologia em Análise e Desenvolvimento de Sistemas · TEADS0347 – Desenvolvimento de Aplicações Multicamadas · Turma NOITE C795NL · 2026.2 · Docente: Márcio Goes do Nascimento



---

## 1. Visão do produto e descrição do problema

### 1.1 Contexto

A Clínica SaúdeCerta controla hoje a agenda em planilhas compartilhadas e mantém os prontuários em papel, guardados em pastas físicas. A direção quer digitalizar o fluxo de atendimento, da marcação da consulta ao registro no prontuário, preservando a privacidade dos dados e permitindo que o sistema cresça por módulos, sem reescrita.

### 1.2 Problema

- Marcações conflitantes no mesmo horário para o mesmo profissional.
- Demora para localizar o histórico de um paciente que retorna.
- Retrabalho da recepção quando o paciente remarca ou cancela.
- Risco no manuseio de dados sensíveis de saúde (LGPD), sem nenhum registro de quem acessou o quê.

### 1.3 Visão do produto

**Para** a recepção, os médicos, os pacientes e a administração da clínica, **que precisam** marcar consultas sem conflito e registrar o atendimento com segurança, o **SaúdeCerta** é um **sistema web de gestão de clínicas** que centraliza cadastros, agenda, prontuário eletrônico, prescrições, exames e relatórios com controle de acesso por perfil e trilha de auditoria. **Diferente** de planilhas e prontuários em papel, o sistema impede conflitos de horário, localiza o histórico em segundos e registra todo acesso a dados clínicos.

### 1.4 Escopo da aplicação

O sistema é dimensionado para uma clínica de médio porte (cerca de 20 profissionais) e foi desenhado para crescer por módulos, sem reescrita.

| Módulo | Responsabilidade (dentro do escopo) |
|---|---|
| Cadastros | Pacientes, profissionais, especialidades e tipos de consulta. |
| Agenda | Disponibilidade dos profissionais; marcação, remarcação e cancelamento de consultas com verificação de conflito. |
| Atendimento / Prontuário | Registro do atendimento no prontuário eletrônico, bloqueio após finalização e adendos. |
| Prescrição e Exames | Emissão de prescrições e solicitação de exames vinculadas a atendimentos em andamento. |
| Relatórios | Relatórios gerenciais para o administrador (consultas por período, profissional e especialidade). |
| Segurança e Auditoria | Autenticação, perfis de acesso (RBAC), controle por vínculo/propriedade e auditoria de acesso. |

**Fora do escopo atual:** faturamento e integração com convênios (o convênio é apenas um dado cadastral do paciente); lembretes de consulta; aplicativo mobile; telemedicina; assinatura digital ICP-Brasil; cobertura de um médico por outro.

### 1.5 Premissas e restrições

- Aplicação web acessada por navegador (cliente SPA em React).
- Java 17+ e Spring Boot 3.x (Spring Web, Spring Data JPA, Spring Security, Bean Validation); H2 em desenvolvimento e PostgreSQL em produção.
- Arquitetura obrigatoriamente multicamadas, organizada em tiers e em camadas internas (monólito modular).
- Implantação em servidor local na própria clínica; os tiers de aplicação e de dados rodam na mesma máquina, mas permanecem separados.
- Equipe de 3 a 4 integrantes, com repositório Git único para código, documentação e ADRs.

---

## 2. Stakeholders e atores do sistema

### 2.1 Stakeholders

| Stakeholder | Papel | Interesse principal |
|---|---|---|
| Direção da clínica | Patrocinador e decisor | Digitalizar o atendimento, reduzir retrabalho e cumprir a LGPD. |
| Recepção | Usuário direto | Marcar e remarcar consultas rapidamente, sem conflito de horário. |
| Profissionais de saúde (médicos) | Usuário direto | Consultar histórico, registrar atendimento, emitir prescrições e solicitar exames com agilidade. |
| Pacientes | Usuário direto | Consultar seus próprios dados, agendamentos e prontuário (somente leitura). |
| Administrador | Usuário direto | Gerenciar usuários, disponibilidade, especialidades, relatórios e acompanhar a auditoria. |
| Equipe de desenvolvimento | Construtor e mantenedor | Arquitetura simples de implementar, testar e evoluir. |
| Legislação (LGPD) | Restrição externa | Dados de saúde são dados pessoais sensíveis e exigem proteção reforçada. |

### 2.2 Atores e perfis de acesso

| Ator | Perfil | Objetivos | Permissões |
|---|---|---|---|
| Recepcionista | `RECEPCAO` | Cadastrar pacientes, marcar, remarcar e cancelar consultas. | Cadastros e agenda. Sem acesso ao conteúdo clínico. |
| Médico | `MEDICO` | Consultar histórico, registrar atendimento, prescrições e exames. | Agenda própria e prontuário dos pacientes com os quais tem vínculo (RN10). |
| Paciente | `PACIENTE` | Visualizar suas informações, consultas e prontuário. | Acesso somente leitura aos próprios dados e histórico (RN12). |
| Administrador | `ADMIN` | Gerir usuários, cadastros, disponibilidade, relatórios e auditoria. | Gestão do sistema, relatórios, leitura de prontuário e consulta à auditoria. |

> **Persona: Márcia, recepcionista.** Atende telefone e balcão ao mesmo tempo, no horário de pico. Precisa marcar uma consulta em menos de um minuto e ser avisada na hora se o horário já estiver ocupado. Usabilidade e resposta rápida são vitais para ela.
