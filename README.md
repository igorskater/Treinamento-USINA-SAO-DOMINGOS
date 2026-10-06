# WF_TI_CAD_USUARIO_FLUIG

Workflow de **Cadastro de Usuario TOTVS Fluig** desenvolvido para a Usina Sao Domingos. Gerencia o ciclo completo de solicitacao, aprovacao e criacao de usuarios na plataforma Fluig.

## Fluxo do Processo

```
SOLICITANTE              GESTOR IMEDIATO           TI                    INTEGRACAO FLUIG
    |                         |                     |                         |
    | Solicitar Cadastro      |                     |                         |
    |------------------------>|                     |                         |
    |                         | Aprovar Criacao     |                         |
    |                         |-------+             |                         |
    |                         |       |             |                         |
    |                         |  [Decisao]          |                         |
    |                         |   /  |  \           |                         |
    |                  Ajustar/   |   \Reprovado    |                         |
    |<-----------------------/    |    \---> FIM     |                         |
    |   (8h para ajustar)         |                 |                         |
    |                             | Aprovado        |                         |
    |                             |---------------->|                         |
    |                             |                 | Validar Solicitacao     |
    |                             |                 |------------------------>|
    |                             |                 |           Criar Usuario Fluig
    |                             |                 |           (automatico)  |
    |<-------------------------------------------------------------------|
    | Notificar e Avaliar Atendimento                                        |
    |---> FIM (Finalizado)                                                   |
```

## Atividades

| # | Atividade | Raia | Tipo |
|---|-----------|------|------|
| 5 | Solicitar Cadastro de Usuario TOTVS FLUIG | SOLICITANTE | Inicio |
| 8 | Aprovar Criacao de Usuario | GESTOR IMEDIATO | Tarefa |
| 10 | Decisao | GESTOR IMEDIATO | Gateway Exclusivo |
| 12 | Validar Solicitacao | TI | Tarefa |
| 16 | Ajustar | SOLICITANTE | Tarefa (prazo: 8h) |
| 22 | Criar Usuario Fluig | INTEGRACAO FLUIG | Servico (automatico) |
| 24 | Notificar e Avaliar Atendimento | SOLICITANTE | Tarefa |
| 29 | Analise erro/Sustentacao | TI | Tarefa |

## Regras de Negocio

### Gateway de Decisao (atividade 10)

O roteamento e baseado no campo `aprovacaoGestor` preenchido pelo Gestor Imediato:

| Valor | Destino |
|-------|---------|
| `Aprovado` | Validar Solicitacao (ativ. 12) |
| `Reprovado` | Fim - Reprovado (ativ. 14) |
| `Ajustar` | Ajustar (ativ. 16) - retorna ao solicitante |

### Prazos

- **Ajustar** (ativ. 16): 8 horas para o solicitante corrigir a solicitacao

### Tratamento de Erro

A atividade **Criar Usuario Fluig** (22) possui um evento intermediario de erro que redireciona para **Analise erro/Sustentacao** (29), permitindo que a equipe de TI investigue e resubmeta.

## Estrutura do Projeto

```
Treinamento USINA SAO DOMINGOS/
├── datasets/
│   └── consultaGrupos.js          # Dataset de consulta de grupos
├── forms/
│   └── Nova Loja/
│       └── Nova Loja.html         # Formulario HTML
├── workflow/
│   ├── .resources/
│   │   ├── WF_TI_CAD_USUARIO_FLUIG.ecm30.xml   # Configuracao do processo
│   │   └── WF_TI_CAD_USUARIO_FLUIG.png          # Imagem do diagrama
│   ├── diagrams/
│   │   └── WF_TI_CAD_USUARIO_FLUIG.process      # Diagrama BPM (BPMN2)
│   └── literals/
│       ├── WF_TI_CAD_USUARIO_FLUIG_pt_BR.properties
│       ├── WF_TI_CAD_USUARIO_FLUIG_en_US.properties
│       └── WF_TI_CAD_USUARIO_FLUIG_es.properties
└── README.md
```

## Raias (Swimlanes)

| Raia | Responsabilidade |
|------|------------------|
| **SOLICITANTE** | Inicia a solicitacao, ajusta quando necessario e avalia o atendimento |
| **GESTOR IMEDIATO** | Aprova, reprova ou solicita ajustes na criacao do usuario |
| **TI** | Valida a solicitacao e analisa erros de integracao |
| **INTEGRACAO FLUIG** | Executa a criacao automatica do usuario no Fluig |

## Finalizacoes do Processo

| Estado Final | Descricao |
|-------------|-----------|
| **Reprovado** (14) | Gestor reprovou a solicitacao |
| **Cancelado** (19) | Solicitante cancelou durante o ajuste |
| **Finalizado** (26) | Usuario criado e atendimento avaliado |

## Pre-requisitos

- TOTVS Fluig (plataforma)
- Fluig Studio (Eclipse) para deploy do processo
- Acesso aos grupos/papeis configurados nas raias do processo

## Deploy

1. Importar o projeto no Fluig Studio (Eclipse Luna SR2/2019-09 + JDK 8)
2. Configurar o servidor Fluig no Eclipse
3. Exportar e publicar o processo via Fluig Studio
4. Configurar os grupos/papeis das raias no Fluig
5. Vincular o formulario ao processo
