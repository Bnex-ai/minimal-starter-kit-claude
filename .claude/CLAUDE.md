# CLAUDE.md — Protocolo Operacional [TEMPLATE]

> Este é um template para uso com o **Claude** (Anthropic).
> Antes de usar em um projeto novo, preencha os slots marcados com
> `<PREENCHER: ...>` e remova este aviso.

---

## Como o Claude descobre este arquivo

O Claude carrega `CLAUDE.md` automaticamente ao iniciar cada sessão, nesta ordem de precedência:

1. **Global** — `~/.claude/CLAUDE.md` (configuração do usuário)
2. **Projeto** — `.claude/CLAUDE.md` na raiz do projeto
3. **Override temporário** — `.claude/CLAUDE.override.md` (tem precedência sobre `CLAUDE.md` no mesmo diretório)

Para sobrescrever instruções temporariamente sem editar este arquivo, crie
`.claude/CLAUDE.override.md` na raiz do projeto.

---

## Bootstrap (Ritual de Entrada) — OBRIGATÓRIO

Leia, NESTA ORDEM, antes de qualquer ação ou resposta substantiva:

0. `.claude/CLAUDE.md` — ESTE arquivo, na íntegra. Não presuma
   conhecê-lo por já ter operado neste projeto antes: sessões
   compactadas perdem o conteúdo original. Releia.
1. `docs/PROJECT_STATUS.md` — fonte única da verdade do estado atual.
2. `wiki/index.md` + entradas de `wiki/log.md` desde a data do STATUS.
3. Condicional — só se o STATUS indicar lacuna ou for a 1ª sessão:
   `docs/project-brief.md`, `docs/prd.md`, `docs/architecture.md`.

A primeira resposta da sessão deve declarar explicitamente:
"Bootstrap: li CLAUDE.md, STATUS (atualizado em DD/MM) e log."

Se a sessão for compactada no meio, REFAÇA o bootstrap ao retomar.

Skip: perguntas triviais (dúvidas pontuais, "como funciona X",
conversas meta sobre o agente) não exigem o ritual — responda direto.

---

## Identidade

- Projeto: `<PREENCHER: [CATEGORIA] - Nome do Projeto>`
- Foco: `<PREENCHER: áreas de atuação, domínios, integrações principais>`
- Tom: `<PREENCHER: técnico e direto / didático / formal / etc.>`
- Idioma: `<PREENCHER: Português do Brasil / English / etc.>`

---

## Regra Principal (Saída)

Ao encerrar a sessão, execute `*session-end`.

A skill em `.claude/skills/session-handoff/SKILL.md` cuida do resto:
1. Atualizar `docs/PROJECT_STATUS.md` com o que foi feito e o próximo passo
2. Registrar em `wiki/log.md` qualquer novo conhecimento gerado na sessão
3. Sincronizar com ferramenta externa (se configurado em `local-config.yaml`)
4. Exibir resumo: o que foi feito, decisões tomadas, próximo passo

Durante a sessão, atualize `docs/PROJECT_STATUS.md` de forma leve sempre que
uma etapa for concluída ou o próximo passo mudar.

---

## Qualificação do Projeto

Rastreamento completo (STATUS + wiki + stories) exige 3+ dos critérios:

| # | Critério |
|---|----------|
| 1 | Tem repositório Git |
| 2 | Múltiplas sessões previstas |
| 3 | Entregável concreto (MVP, script, automação) |
| 4 | 2+ tecnologias |
| 5 | Uso comercial ou cliente final |

Se o projeto não qualifica, ignore as seções de Wiki e Documentos estendidos
— use apenas `PROJECT_STATUS.md`.

---

## Nomenclatura

Padrão: `[CATEGORIA] - [Nome do Projeto]`
Categorias: `APP` · `API` · `SCRIPT` · `INFRA` · `ANÁLISE` · `AI`

---

## Documentos

| Arquivo | Quando atualizar |
|---------|------------------|
| `docs/PROJECT_STATUS.md` | Toda sessão (leve durante, completo no `*session-end`) |
| `docs/project-brief.md` | Início do projeto |
| `docs/prd.md` | Ao definir requisitos |
| `docs/architecture.md` | A cada decisão técnica relevante |
| `docs/stories/epics/` | A cada nova story de desenvolvimento |

---

## LLM Wiki — Operações

Se o projeto qualifica (3+ critérios), use INGEST / QUERY / LINT.

### INGEST — adicionar fonte
1. Usuário coloca arquivo em `raw/sources/`
2. Leia e extraia takeaways principais
3. Crie/atualize: `wiki/sources/[Date]-[Title].md`, `wiki/concepts/*`,
   `wiki/entities/*`, `wiki/index.md`, `wiki/log.md`
4. Atualize `inbound_links` nas páginas referenciadas

### QUERY — pergunta contra o wiki
1. Procure em `wiki/index.md` e leia páginas relevantes
2. Sintetize resposta com citações
3. Se a resposta é valiosa, salve em `wiki/analysis/[Question].md`
4. Registre em `wiki/log.md`

Resposta valiosa = nova página. Explorações compõem.

### LINT — health check (a cada 5-10 ingestões)
- Contradições entre páginas → marcar `[CONTRADICTION]`
- Páginas órfãs (sem `inbound_links`)
- Conceitos mencionados sem página própria
- Cross-references quebradas
- Sugerir novas fontes
- Atualizar stats em `wiki/index.md`

Append report em `wiki/log.md`.

---

## Segredos

- Chaves de API → `.env` (gitignored)
- Config de máquina → `local-config.yaml` (gitignored)
- Arquivos de credenciais → `secrets/` (gitignored)
- Modelo público → `.env.example` (commitado)
- **NUNCA** commite `.env`, `local-config.yaml` ou qualquer arquivo dentro de `secrets/`

---

## Protocolo de Segurança — Arquivos Sensíveis

### Gatilhos de ativação

Este protocolo é ativado automaticamente quando o usuário:

- Pede explicitamente para **proteger**, **mover**, **guardar** ou **realocar** um arquivo
- Menciona **credencial**, **chave**, **token**, **secret**, **API key**, **service account**, **senha**
- Compartilha conteúdo com padrões sensíveis: `"key"`, `"token"`, `"secret"`, `"password"`, `"client_secret"`, `"private_key"`
- Menciona extensões típicas de credenciais: `.json`, `.yaml`, `.env`, `.pem`, `.p12`, `.key`

### Pasta segura do projeto

```
secrets/        ← destino padrão para qualquer arquivo sensível
```

Já está no `.gitignore`. Criar se não existir.

### Sequência obrigatória (5 passos — nunca pular)

```
1. MOVER
   → Mover o arquivo para secrets/<nome-do-arquivo>
   → Criar a pasta secrets/ se não existir

2. GITIGNORE
   → Verificar se secrets/ já está no .gitignore
   → Se não estiver: adicionar a entrada

3. HISTÓRICO GIT
   → Executar: git status
   → Se o arquivo já foi trackeado (aparece em "tracked files"):
     - Executar: git rm --cached <arquivo>
     - Avisar: "Este arquivo estava no histórico do git. Considere rotacionar a chave."

4. .env.example
   → Atualizar .env.example com referência ao novo path ou variável:
     CREDENTIALS_PATH=secrets/<nome-do-arquivo>

5. CONFIRMAR
   → Informar ao usuário:
     - O que foi movido e para onde
     - Se estava exposto no git
     - Se a chave deve ser rotacionada
     - Próximo passo recomendado
```

**NUNCA execute apenas o passo 1.** Mover sem verificar o histórico git e o .gitignore é falsa segurança. O protocolo é sempre os 5 passos completos.

---

## Fronteiras e Disciplina Operacional

### Alterações diretas em ambientes vivos

`<PREENCHER: ambientes de execução do projeto — ex. VPS, servidor de
automação, banco de dados gerenciado, plataforma no-code>`

É PERMITIDO alterar diretamente esses ambientes — eles são a
verdade operacional e não vivem no git.

Condição inegociável: toda alteração direta gera, na MESMA sessão,
antes de mudar de assunto:

1. entrada em `docs/PROJECT_STATUS.md` (o que mudou, onde, quando)
2. entrada em `wiki/log.md` e, se gerou conhecimento novo, na
   página correspondente em `wiki/entities/` ou `wiki/concepts/`

Uma alteração viva sem registro é um GAP. Se o contexto acabar
antes do registro, o gap fica invisível na próxima sessão e o
projeto passa a operar sobre informação falsa.

Ao encerrar: confirmar que não há alteração viva sem registro.

### Fronteira de execução — container vs. máquina do usuário

Ambientes distintos. Nunca confundir:

| Ambiente | O que é | Papel |
|---|---|---|
| Container do agente | sandbox efêmero na nuvem | rascunho — nunca é referência |
| Máquina do usuário | pasta local do projeto | FONTE DA VERDADE de backend/infra/docs |
| GitHub — repo do projeto | `<PREENCHER: público ou privado>` | espelho do que foi APROVADO |
| Repos escritos por ferramenta externa | ex.: builders no-code que sincronizam sozinhos | espelho automático; NÃO passa pelo rito |
| `<PREENCHER: ambiente(s) de produção>` | produção | verdade operacional |

**REGRA:** operações que criam, movem ou APAGAM arquivos em lote na
máquina do usuário — `git init`, `git add`, `git rm`, `mv`, `rm`,
scripts de reorganização — NÃO são executadas pela ponte remota.

Motivo técnico: a ponte não consegue remover arquivos
("Operation not permitted"). O git depende de remover arquivos
temporários (`index.lock`, objetos parciais). Rodar git pela ponte
deixa o repositório inconsistente e o agente não consegue limpar
o que sujou.

Procedimento correto:

1. Rascunhar/validar no container do agente
2. Entregar ao usuário o comando exato, pronto para colar
3. O usuário executa nativamente (PowerShell / terminal)
4. O agente confirma o resultado por LEITURA — `ls`, `git diff`,
   `git log` e **`git --no-optional-locks status`**.
   NUNCA `git status` puro pela ponte: ele atualiza o índice, cria
   `.git/index.lock`, e a ponte não consegue remover o lock
   (`Operation not permitted`) — o repositório fica travado para o
   próximo comando do usuário. A flag `--no-optional-locks` existe
   exatamente para consultar status sem tomar lock.

A ponte é para LER, EDITAR arquivo a arquivo e ESCREVER arquivos
novos. Não é para gerenciar repositório.

Repos escritos por ferramenta externa têm autor próprio. Ao
inspecionar o histórico, esperar commits que ninguém desta
conversa aprovou — normal nesses repos, ANOMALIA no repo do projeto.

Arquivo apagável pela ponte que precisa ser removido: mova para
`_to_delete/` (gitignorado) em vez de tentar `rm` — a ponte não
remove arquivos. O usuário apaga a pasta manualmente quando quiser.

### Regra anti-deriva — arquivos que existem em mais de um lugar

`<PREENCHER: arquivo que existe no repo E num ambiente de execução,
ex. script/skill que vive no repositório e também precisa estar
instalado em produção>`.

Sentido único de propagação: **repo → ambiente. NUNCA o inverso.**

Antes de editar qualquer arquivo desses:

1. Comparar checksum das duas cópias (`sha256sum`)
2. Se divergirem, PARAR e reportar antes de editar

Depois de propagar:

3. Comparar checksum de novo e registrar o valor no STATUS

Editar a cópia do ambiente diretamente cria uma versão fantasma
que ninguém sabe que existe.

### Verificação de existência — nunca concluir por ausência

Ao verificar se algo existe (repo, workflow, tabela, projeto,
arquivo), declarar SEMPRE o escopo consultado e o que ficou fora
do alcance.

```
Errado:  "só existe um repositório"
Certo:   "a API pública mostra 1 repo; repos privados não aparecem
          sem autenticação — confirme na interface logada"
```

Vale para: GitHub sem token, MCP autenticado numa conta só,
listagens filtradas por permissão, buscas que dependem de índice.

Exemplo ilustrativo: uma sessão consultou a API pública do GitHub e
concluiu que havia 1 repositório. Havia 3 — dois privados, que a
API sem autenticação não lista. A pergunta original era justamente
sobre evitar duplicação.

---

## Comandos

| Comando | Função |
|---------|--------|
| `*session-end` | Finaliza sessão, atualiza STATUS e wiki/log.md, sincroniza externamente, prepara retomada |
| `*sync-status` | Apenas sincroniza PROJECT_STATUS.md sem encerrar sessão |
| `*wiki ingest` | Inicia operação INGEST no wiki |
| `*wiki query [pergunta]` | Consulta o wiki e retorna síntese |
| `*wiki lint` | Executa health check do wiki |
