# Minimal Starter Kit — Claude Edition

Boilerplate completo para iniciar qualquer projeto com o **Claude** (Anthropic), incluindo:

- Protocolo de bootstrap (ritual de entrada por sessão)
- Base de conhecimento persistente (LLM Wiki)
- Controle de estado do projeto
- Sincronização externa opcional (Notion, Trello, Google Drive, Linear, etc.)
- Segurança de segredos

Funciona com qualquer stack. Agnóstico de linguagem e framework.

Licenciado sob [MIT](LICENSE) — livre para uso pessoal e comercial.

---

## Estrutura

```
minimal starter kit claude/
├── .claude/
│   ├── CLAUDE.md                          ← instrução principal para o Claude
│   ├── CLAUDE.override.md                 ← override temporário (criar se necessário)
│   └── skills/
│       └── session-handoff/
│           ├── SKILL.md                   ← skill de encerramento de sessão (*session-end)
│           ├── providers.md               ← referência de sync por provider
│           └── status-template.md         ← template interno do agente
├── docs/
│   ├── PROJECT_STATUS.md                  ← estado atual do projeto (atualizado a cada sessão)
│   ├── project-brief.md                   ← escopo e problema
│   ├── prd.md                             ← requisitos do produto
│   ├── architecture.md                    ← decisões técnicas
│   ├── reference/
│   │   └── workshop-github.md             ← guia de publicação segura no GitHub
│   └── stories/
│       └── epics/                         ← rastreamento de desenvolvimento
├── wiki/
│   ├── index.md                           ← catálogo de todo o conhecimento
│   ├── log.md                             ← timeline de operações
│   ├── entities/                          ← pessoas, organizações, produtos, sistemas
│   ├── concepts/                          ← ideias, teorias, frameworks, padrões
│   ├── sources/                           ← resumos de fontes ingeridas
│   └── analysis/                          ← perguntas respondidas, sínteses
├── raw/
│   ├── sources/                           ← documentos brutos para ingestão no wiki
│   └── assets/                            ← imagens e arquivos de dados
├── secrets/                               ← credenciais (gitignored)
├── .env.example                           ← modelo de variáveis de ambiente
├── .gitignore                             ← proteção completa de segredos
├── local-config.yaml.template             ← configuração local da máquina
├── LICENSE                                ← licença MIT
└── README.md
```

---

## Como usar

### 1. Obtenha o starter kit a partir do GitHub

**Opção A — Use this template (recomendado):** clique em "Use this template" no repositório do GitHub para criar um novo repositório próprio, já limpo, sem histórico do original.

**Opção B — Clone direto:**

```bash
git clone https://github.com/<sua-org>/minimal-starter-kit-claude.git meu-novo-projeto
cd meu-novo-projeto/
rm -rf .git
git init
```

### 2. Preencha o CLAUDE.md

Abra `.claude/CLAUDE.md` e preencha os slots `<PREENCHER: ...>` com as informações do projeto.

> **Override temporário:** Para sobrescrever instruções sem editar `CLAUDE.md`,
> crie `.claude/CLAUDE.override.md` na raiz. O Claude sempre prefere o arquivo
> `CLAUDE.override.md` ao `CLAUDE.md` no mesmo diretório.

### 3. Configure as variáveis de ambiente

```bash
cp .env.example .env
# edite .env com seus valores reais
```

### 4. Configure sua máquina local (opcional)

```bash
cp local-config.yaml.template local-config.yaml
# edite local-config.yaml conforme suas preferências
```

Para habilitar sync com ferramenta externa (Notion, Trello, etc.):

```yaml
# local-config.yaml
external_sync:
  enabled: true
  provider: notion        # ou trello, google_drive, airtable, linear...
  credentials_env: SYNC_API_KEY
  destination_id_env: SYNC_DESTINATION_ID
```

Adicione as credenciais no `.env`:

```
SYNC_API_KEY=sua_chave_aqui
SYNC_DESTINATION_ID=id_do_destino_aqui
```

### 5. Preencha os documentos base

Antes de escrever qualquer linha de código:

1. `docs/project-brief.md` — defina o problema e o escopo
2. `docs/prd.md` — detalhe os requisitos
3. `docs/architecture.md` — registre as decisões técnicas

### 6. Ao encerrar cada sessão de trabalho

Execute no Claude:

```
*session-end
```

O agente irá:
- Atualizar `docs/PROJECT_STATUS.md`
- Registrar em `wiki/log.md`
- Sincronizar com sua ferramenta externa (se configurado)
- Exibir resumo do que foi feito e o próximo passo

---

## Comandos disponíveis

| Comando | Função |
|---------|--------|
| `*session-end` | Encerra sessão, atualiza STATUS + wiki, sincroniza |
| `*sync-status` | Sincroniza apenas o PROJECT_STATUS.md |
| `*wiki ingest` | Processa arquivo em `raw/sources/` para o wiki |
| `*wiki query [pergunta]` | Consulta o wiki e retorna síntese |
| `*wiki lint` | Health check do wiki (contradições, órfãos, etc.) |

---

## LLM Wiki

O wiki é uma base de conhecimento persistente que cresce ao longo das sessões.

- **INGEST** — coloque um documento em `raw/sources/` e execute `*wiki ingest`
- **QUERY** — faça perguntas contra o conhecimento acumulado com `*wiki query`
- **LINT** — valide a consistência do wiki a cada 5-10 ingestões com `*wiki lint`

---

## Critérios de qualificação

Rastreamento completo (STATUS + wiki + stories) é recomendado para projetos com **3 ou mais** critérios:

- Tem repositório Git
- Tem mais de uma sessão de trabalho prevista
- Tem entregável concreto (MVP, script, automação, relatório)
- Envolve 2 ou mais tecnologias
- Tem uso comercial ou cliente final

Para scripts rápidos ou testes em sandbox, use apenas `PROJECT_STATUS.md`.

---

## Nomenclatura recomendada

Padrão: `[CATEGORIA] - [Nome do Projeto]`

| Categoria | Uso |
|-----------|-----|
| `APP`     | Aplicações web ou mobile |
| `API`     | Serviços e integrações |
| `SCRIPT`  | Automações e ferramentas CLI |
| `INFRA`   | DevOps e infraestrutura |
| `ANÁLISE` | Data analysis e relatórios |
| `AI`      | Projetos com modelos de linguagem |

---

## Segurança

- `.env` — **nunca commitar** — contém suas chaves reais
- `local-config.yaml` — **nunca commitar** — configuração da máquina
- `.env.example` — commitar — é o contrato público das variáveis necessárias
- `local-config.yaml.template` — commitar — é o modelo para novos devs

---

## Providers suportados para sync externo

| Provider | Status |
|----------|--------|
| Notion | ✅ Suportado via MCP |
| Trello | ✅ Suportado via API REST |
| Google Drive | ✅ Suportado via MCP |
| Airtable | ✅ Suportado via API REST |
| Linear | ✅ Suportado via API GraphQL |
| GitHub Projects | ✅ Suportado via `gh` CLI |
| Apenas local | ✅ Padrão (sem configuração) |
