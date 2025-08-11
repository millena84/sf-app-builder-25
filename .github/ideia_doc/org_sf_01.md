# modelo 01

---

1) _docs/ — diretrizes, visão e como trabalhar

Propósito: concentrar tudo que orienta pessoas e time: arquitetura, governança, procedimentos e releases. É o “manual de uso” da org.

O que entra (e por quê)

arquitetura/
Diagramas (C4, fluxos, integrações), decisões arquiteturais (ADRs). Ajuda novos(as) devs a entenderem o todo rápido.

governanca/
Padrões de desenvolvimento (Apex/LWC/Flows), segurança (profiles/permission sets, Named Credentials), nomenclatura, critérios de Definition of Ready/Done, revisão de código/flow, e compliance.

procedimentos/
Runbooks e SOPs: passo-a-passo para tarefas repetíveis (ex.: “criar sandbox”, “migrar flow”, “rotina de backup de relatórios”, “como solicitar Named Credential”).

referencias/
Links oficiais, trilhas Trailhead, checklists e cheatsheets — um hub de aprendizado e consulta.

releases/
Notas de versão e planos de release (mesmo que o deploy esteja no Flosum, o histórico e impactos ficam aqui, versionados).


Modelos .md

_docs/arquitetura/ADR-YYYYMMDD-titulo.md

# ADR: [Título da decisão]
- **Data:** 2025-08-11
- **Status:** Proposta | Aprovada | Obsoleta
- **Contexto**  
  [Cenário, restrições, requisitos não-funcionais]
- **Decisão**  
  [O que foi decidido e por quê]
- **Alternativas consideradas**  
  - Alternativa A (prós/contras)
  - Alternativa B (prós/contras)
- **Consequências**  
  [Trade-offs, impactos em operação, segurança, custos]
- **Rastreabilidade**  
  [Releases/épicos/tickets relacionados]

_docs/governanca/padroes-desenvolvimento.md

# Padrões de Desenvolvimento e Qualidade
## Nomenclatura
- Objetos: `WW2_MeuObjeto__c` | Campos: `Canal__c`
- Flows: `FLW_[Escopo]_[Ação]` (ex.: `FLW_JT_CreateChecklist`)
## Segurança por padrão
- Apex: `with sharing`, FLS/CRUD enforcement
- Named Credentials para callouts
## Flows
- Subflows para reuso, **1 responsabilidade por Flow**
- Tratamento de falhas: Fault Paths + Logging
## LWC
- Props tipadas, events documentados, teste com Jest
## Revisão
- Checklist de PR/CR (mesmo via Flosum), cobertura mín. de testes, linters

_docs/procedimentos/runbook-criar-sandbox.md

# Runbook — Criar Sandbox
## Objetivo
Criar Sandbox [tipo] com [dados/metadados].
## Pré-requisitos
- Aprovação de [papel]
- Janela de mudança
## Passo a passo
1. [Ação detalhada…]
2. …
## Validação pós-execução
- [Checks objetivos]
## Rollback
- [Plano prático]
## Responsáveis
- Solicitante / Executor / Aprovador
## Anexos
- Prints, links, evidências

_docs/releases/RELEASE-2025.08.md

# Release 2025.08
**Data:** 2025-08-30  
**Janela:** 22:00–23:00 BRT  
**Resumo:** [objetivo da release]

## Itens
- [JT] Novo CHKL com vínculo automático (JIRA-123)
- [Seg] Ajuste validação SMS (JIRA-456)

## Impactos
- Objetos: JT__c, CHKL__c
- Flows: FLW_JT_CreateChecklist
- Permissões: permset [nome]

## Riscos e Mitigações
- [risco] → [mitigação]

## Plano de Rollback
- [passos]

## Evidências de Teste
- Link para `_docs/procedimentos/evidencias/2025.08/…`


---

2) md/ — documentação dos componentes (o “catálogo técnico”)

Propósito: documentação granular por artefato (objeto, flow, apex, LWC, relatório, integração). Isso acelera troubleshooting, revisão e onboarding.

Subpastas (e por quê)

objetos/ — modelo de dados, campos, validações, relacionamentos, onde é usado.

flows/ — tipo, variáveis, invocações, fault handling, limites, dependências.

apex/ — classes/triggers, responsabilidades, métodos públicos, bulkificação, testes.

lwc/ — props, events, wire/adapters, chamadas Apex, caching, slots.

relatorios_dashboards/ — filtros, running user, assinaturas, origem dos dados.

integracoes/ — contratos de API, autenticação, Named Credentials, limites, idempotência.


Modelos .md

md/objetos/JT__c.md

# JT | JT__c
## Resumo
[O que representa no negócio e como é usado]
## Campos principais
| Campo (API) | Label | Tipo | Obrigatório | Observações |
|-------------|-------|------|-------------|-------------|
| Nome        | Name  | Auto Number | Sim | Prefixo JT-
| CHKL__c     | CHKL  | Lookup(CHKL__c) | Não | Preenchido por LWC

## Relacionamentos
- Lookup → CHKL__c (1:N) — usado para [propósito]
- [Outros]

## Validações/Regras
- [Nome Regra]: [Descrição]

## Automação Associada
- Flows: `FLW_JT_CreateChecklist`
- Triggers: `JTTrigger` (se houver)

## Uso em Telas/Processos
- [Flexipage/Record Page], [Lightning App]

## Segurança
- Visibilidade por perfil/permset: [detalhes]

## Histórico de Alterações
| Data | Autor | Mudança | Release |
|------|-------|---------|---------|
| 2025-08-05 | Millena | Inclusão do lookup CHKL__c | 2025.08 |

md/flows/FLW_JT_CreateChecklist.md

# Flow: FLW_JT_CreateChecklist
## Tipo
Record-Triggered (after insert/update)
## Objetivo
Criar CHKL e vincular ao JT quando [condições].
## Entradas/Saídas
- Entradas: `$Record` (JT__c)
- Saídas: ID do CHKL criado
## Passos-chave
1. Decision [condição]
2. Create Records (CHKL__c)
3. Update `$Record.CHKL__c`
## Fault Handling
- Fault path → Log em `Log__c`, notificação para `Queue_Suporte`
## Dependências
- Objeto: JT__c, CHKL__c
- Permissões: `perm_FlowChecklist`
## Limitações/Observabilidade
- Volume: até [X]/hora
- Métricas (SLI): taxa de sucesso, tempo médio
## Testes
- Cenários, evidências e links
## Histórico de Alterações
[como no modelo anterior]

md/apex/CHKLService.md

# Apex: CHKLService
## Responsabilidade
[Única responsabilidade da classe]
## Assinatura Pública
```apex
public with sharing class CHKLService {
  public static Id createForJt(Id jtId, String observacao) { ... }
}

Regras de Uso

Comportamento em bulk: aceita lista de JT? [sim/não]

FLS/CRUD: verificação via Security.stripInaccessible


Erros e Exceções

CHKLServiceException quando [condição]


Testes

CHKLService_Test (cobertura atual: 92%)


Dependências

Named Credentials? [n/a]

Outros serviços/utilitários


Histórico de Alterações

[como no modelo anterior]

#### `md/lwc/jtChecklist.md`
```markdown
# LWC: jtChecklist
## Props
- `recordId: string` (JT)
- `observacao: string?`
## Eventos
- `saved` (`detail: { chklId }`)
## Backend
- Apex: `CHKLService.createForJt`
## UI/UX
- Exibe campos [..], mostra toast de sucesso/erro
## Acessibilidade
- ARIA roles, focus management
## Testes
- Jest: [cenários]
## Histórico
[...]

md/integracoes/CRM_Checklist_API.md

# Integração: CRM Checklist API
## Caso de uso
[por quê existe]
## Contrato (Swagger/OpenAPI)
- Arquivo: `/md/integracoes/contracts/checklist-api.yaml`
## Autenticação
- Named Credential: `nc_checklist`
## Limites/SLAs
- 200 req/min, timeout 5s, retry com jitter (x3)
## Erros
- Mapeamento HTTP → erro de domínio
## Idempotência
- Chave: `JT__c:Id`
## Observabilidade
- Correlation-Id, logs, dashboards de falhas
## Segurança
- PII? DLP? Criptografia em repouso?
## Histórico
[...]


---

3) logs/ — trilhas e memória operacional

Propósito: registrar fatos importantes ao longo do tempo: auditorias de mudanças e erros conhecidos. Ajuda suporte, RDM e pós-mortem.

O que entra

auditoria/
Mudanças relevantes (ex.: ativação de flow, alteração crítica de permissão, criação de Named Credential). Útil para RCA.

erros_conhecidos/
Known Error Database (KEDB): sintomas, causa raiz (se houver), workaround e status da correção.


Modelos .md

logs/auditoria/2025-08-11-mudancas-criticas.md

# Auditoria — 2025-08-11
## Itens
- Ativado `FLW_JT_CreateChecklist` em Prod (22:15 BRT) — Change #CHG-00123 — Aprovador: [nome]
- Atualizado permset `perm_FlowChecklist` — inclusão de `read/write` em CHKL__c
## Evidências
- [links e prints]

logs/erros_conhecidos/KEDB-0007-flow-jt-falha-lookup.md

# KEDB-0007 — Flow JT falha ao preencher lookup CHKL__c
## Sintomas
Erro “Campo inexistente” ao salvar JT.
## Ambiente
Sandbox UAT
## Causa Suspeita
Implantação parcial sem campo CHKL__c
## Workaround
Desativar Flow, criar campo, reativar
## Plano Definitivo
Incluir dependências no pacote Flosum e validar antes do deploy
## Status
Aberto | Mitigado | Resolvido
## Referências
- Ticket JIRA-789
- Logs: [link]


---

4) org/ — espelho lógico da organização

Propósito: um índice navegável dos artefatos (não é para deploy pelo Git), facilitando encontrar “onde vive” cada coisa e cruzar com a doc do md/.

O que entra

Estrutura por tipo: objetos_custom/, objetos_standard/, codigo/{apex,triggers,lwc,aura,visualforce}, fluxos/, relatorios/, etc.

Índices (README.md em cada subpasta) listando artefatos com links para os .md correspondentes em md/.


Modelo de índice

# Objetos Custom
| Objeto | API | Doc |
|-------|-----|-----|
| JT    | JT__c | [/md/objetos/JT__c.md](../../md/objetos/JT__c.md) |
| CHKL  | CHKL__c | [/md/objetos/CHKL__c.md](../../md/objetos/CHKL__c.md) |


---

(Opcional) json/ — suporte técnico para automação

Propósito: arquivos auxiliares consumidos por scripts (geração de docs, relatórios, mapas).

config_org.json — ID da org, My Domain, versões de pacotes, políticas (sem segredos!).

mapeamento_componentes.json — tipo de metadata → pasta de documentação (alinha com seu mapeamento já existente).


Exemplo:

{
  "org": {
    "name": "DevHub - Millena",
    "myDomain": "org-millena84-p001-dev-ed",
    "apiVersion": "61.0"
  },
  "map": {
    "CustomObject": "md/objetos",
    "Flow": "md/flows",
    "ApexClass": "md/apex",
    "LightningComponentBundle": "md/lwc"
  }
}


---

GitHub Pages — como publicar bem (e sem dor)

Você tem duas rotas “de mercado”:

Rota A — Jekyll nativo do GitHub Pages (simples e sem CI complexo)

Quando usar: site leve, sem necessidade de temas avançados, manutenção mínima.

1. Crie a pasta /docs no repo (o Pages pode servir direto dela).


2. Mantenha o conteúdo canônico em md/ e _docs/, e copie para /docs o que será publicado (mesma hierarquia ou curado).


3. Adicione um docs/_config.yml simples (tema padrão) e um docs/index.md como portal.


4. Ative Pages: Settings → Pages → Source = “Deploy from a branch”, branch = main, folder = /docs.



Vantagens: zero pipeline.
Cuidados: pode gerar duplicação (origem em md/ e cópia em docs/). Mitigue com um script local para sincronizar.

Exemplo de docs/index.md:

# Documentação da Organização Salesforce

## Comece por aqui
- [Visão de Arquitetura](/arquitetura/index.md)
- [Governança e Padrões](/governanca/padroes-desenvolvimento.md)
- [Procedimentos (Runbooks)](/procedimentos/index.md)
- [Catálogo de Componentes](/catalogo/index.md)
- [Releases](/releases/index.md)

> **Nota:** Deploys são feitos via Flosum. Este site é apenas documentação.

Rota B — MkDocs + Material (recomendada em muitas empresas)

Quando usar: navegação melhor, busca ótima, estética profissional, nav por seções, admonitions, etc.

Como fazer (didático):

1. Crie /docs-site/ para ser a fonte do site (mantemos md/ e _docs/ como origem “bruta”).


2. mkdocs.yml define navegação; você pode referenciar arquivos copiados de md/ e _docs/.


3. GitHub Actions para build e deploy automático na branch gh-pages.


4. Sincronização de conteúdo: no workflow, copie de md/ e _docs/ apenas o que vai pro site (ex.: rsync para docs-site/).



Exemplo de mkdocs.yml:

site_name: Docs Salesforce Org
theme:
  name: material
nav:
  - Início: index.md
  - Arquitetura:
      - Visão: _docs/arquitetura/visao.md
      - ADRs:
          - ADRs: _docs/arquitetura/index-adrs.md
  - Governança:
      - Padrões: _docs/governanca/padroes-desenvolvimento.md
      - Segurança: _docs/governanca/seguranca.md
  - Catálogo:
      - Objetos:
          - JT__c: md/objetos/JT__c.md
          - CHKL__c: md/objetos/CHKL__c.md
      - Flows:
          - FLW_JT_CreateChecklist: md/flows/FLW_JT_CreateChecklist.md
  - Releases:
      - 2025.08: _docs/releases/RELEASE-2025.08.md

Workflow (.github/workflows/docs.yml) — visão conceitual:

name: Build & Publish Docs
on:
  push:
    branches: [ main ]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Preparar fontes para site
        run: |
          rm -rf docs-site && mkdir -p docs-site
          rsync -av _docs/ docs-site/_docs/
          rsync -av md/ docs-site/md/
          cp mkdocs.yml docs-site/
      - name: Setup Python
        uses: actions/setup-python@v5
        with: { python-version: '3.x' }
      - name: Install MkDocs
        run: pip install mkdocs mkdocs-material
      - name: Build
        run: |
          cd docs-site
          mkdocs build
      - name: Deploy to gh-pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: docs-site/site

Boas práticas para o Pages (vale para A e B):

Não publique segredos (URLs sensíveis, tokens, credenciais).

Sanitize screenshots (mascarar dados de clientes).

Link Checker (GitHub Action de verificação de links quebrados).

markdownlint (padroniza estilo).

TOC e navegação por seção (os modelos acima já ajudam).

Aviso de escopo no topo do site (“Deploys via Flosum; repositório é de documentação”).

Versões de docs: quando fizer releases grandes, gere uma cópia das notas (e eventualmente da documentação impactada) por versão/ramo — ou use prefixos RELEASE-YYYY.MM.



---

Dicas finais (práticas, pensando no seu dia a dia)

Comece pela estrutura mínima e vá preenchendo sempre que um artefato entrar em produção.

Padronize o histórico (mesas colunas, sempre) — acelera auditoria.

Ganchos de aprendizagem: em cada .md, inclua 1–2 links oficiais (Trailhead/Help) para reforçar memorização do tema.

Integra Flosum com links: nos .md de componentes, inclua o ID/URL do item do Flosum (se aplicável) — ajuda o time a cruzar informação rapidamente.



