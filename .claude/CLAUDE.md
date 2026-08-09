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

## Bootstrap (Ritual de Entrada)

Ao iniciar uma sessão de trabalho, antes de qualquer ação ou resposta
substantiva, reconstrua o estado do projeto nesta ordem:

1. `docs/PROJECT_STATUS.md` — fonte única da verdade do estado atual.
2. `wiki/index.md` + últimas entradas de `wiki/log.md` desde a data
   registrada no STATUS — conhecimento acumulado recente.
3. Condicional — leia apenas se o STATUS indicar lacuna ou se for a 1ª
   sessão: `docs/project-brief.md`, `docs/prd.md`, `docs/architecture.md`.

Primeira resposta deve conter:
- Resumo de 2-3 linhas do estado atual
- Último ponto de parada
- Próximo passo sugerido (aguardando validação)

Skip do bootstrap: perguntas triviais (dúvidas pontuais, "como funciona X",
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

## Comandos

| Comando | Função |
|---------|--------|
| `*session-end` | Finaliza sessão, atualiza STATUS e wiki/log.md, sincroniza externamente, prepara retomada |
| `*sync-status` | Apenas sincroniza PROJECT_STATUS.md sem encerrar sessão |
| `*wiki ingest` | Inicia operação INGEST no wiki |
| `*wiki query [pergunta]` | Consulta o wiki e retorna síntese |
| `*wiki lint` | Executa health check do wiki |
