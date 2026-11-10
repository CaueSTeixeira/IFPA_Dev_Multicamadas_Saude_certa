# SaúdeCerta — Sistema de Gestão de Clínica

Estudo de caso da disciplina **TEADS0347 – Desenvolvimento de Aplicações Multicamadas** (IFPA · Tecnologia em Análise e Desenvolvimento de Sistemas · Turma NOITE C795NL · 2026.2). Docente: Márcio Goes do Nascimento.

O SaúdeCerta é um sistema web multicamadas (Java, Spring Boot, React, PostgreSQL) que digitaliza o atendimento de uma clínica: cadastros, agenda de consultas, prontuário eletrônico, prescrições, exames, relatórios, controle de acesso por perfil e auditoria.

## Equipe

| Integrante | Usuário no GitHub |
|---|---|
| [CAUE DE SOUZA TEIXEIRA] | [@CaueSTeixeira] |
| [JUAN PABLO MESCOUTO DA SILVA] | [@JuanSilva078] |
| [HERLEI CAUÃ PIMENTEL DA SILVA] | [@hcps01] |
| [LEONEI MACIEL CARDOSO JÚNIOR] | [@leoneimaciel] |

## Entregas

| Entrega | Conteúdo | Onde está | Status |
|---|---|---|---|
| E1 | Visão e requisitos | [`docs/visao.md`](docs/visao.md), [`docs/requisitos.md`](docs/requisitos.md) | Concluída |
| E2 | Projeto arquitetural (C4, camadas, domínio, ADRs, rastreabilidade) | [`docs/arquitetura.md`](docs/arquitetura.md), [`docs/adr/`](docs/adr/), [`docs/rastreabilidade.md`](docs/rastreabilidade.md) | Concluída |
| E3 | Protótipo em Spring Boot | `src/` | Pendente |
| Projeto Final | Serviço extraído, auditoria assíncrona, defesa | `src/`, `docs/` | Pendente |

Os PDFs entregues estão em [`docs/E1_Visao_e_Requisitos.pdf`](docs/E1_Visao_e_Requisitos.pdf) e [`docs/E2_Projeto_Arquitetural.pdf`](docs/E2_Projeto_Arquitetural.pdf).

## Estrutura do repositório

```
├── README.md
├── docs/
│   ├── visao.md                  (E1)
│   ├── requisitos.md             (E1)
│   ├── arquitetura.md            (E2, C4, camadas, domínio, API)
│   ├── diagramas/                (E2, imagens dos diagramas)
│   ├── adr/                      (E2, decisões arquiteturais)
│   ├── rastreabilidade.md        (E2)
│   ├── E1_Visao_e_Requisitos.pdf
│   └── E2_Projeto_Arquitetural.pdf
├── src/                          (E3 e Projeto Final, Spring Boot)
└── .gitignore
```

## Como executar

A implementação começa na E3. Esta seção será preenchida com os pré-requisitos (Java 17+, Maven) e os comandos para subir o sistema localmente com H2.
