---
name: syncing-session-status
description: Atualiza docs/PROJECT_STATUS.md e wiki/log.md com o estado atual da sessão, e sincroniza com ferramenta externa (Notion, Trello, Google Drive, Airtable, Linear, GitHub Projects). Use ao encerrar uma sessão de trabalho, ao executar *session-end ou *sync-status, ou quando o usuário pedir para registrar progresso do projeto.
---

# Syncing Session Status

Atualiza o estado do projeto localmente e sincroniza com ferramenta externa se configurada.

## Fluxo de trabalho

Copie e marque conforme avança:

```
Progresso:
- [ ] Etapa 1: Coletar informações da sessão
- [ ] Etapa 2: Atualizar docs/PROJECT_STATUS.md
- [ ] Etapa 3: Registrar em wiki/log.md
- [ ] Etapa 4: Sincronizar externamente (se habilitado)
- [ ] Etapa 5: Confirmar ao usuário
```

### Etapa 1: Coletar

Responda antes de escrever qualquer arquivo:

- O que foi concluído nesta sessão?
- Qual o próximo passo concreto e acionável?
- Há bloqueios ativos?
- Qual o status geral agora? (`Não iniciado` / `Em andamento` / `Em revisão` / `Concluído`)
- Alguma decisão técnica importante foi tomada?
- Algum novo conhecimento foi gerado que vale registrar no wiki?

### Etapa 2: Atualizar PROJECT_STATUS.md

Use o template em [status-template.md](status-template.md) para preencher os campos.
Substitua o conteúdo da sessão anterior. Apenas adicione linhas ao Histórico — nunca remova.

### Etapa 3: Registrar em wiki/log.md

Adicione uma entrada no final de `wiki/log.md`:

```markdown
## YYYY-MM-DD — SESSION
**Operação:** Encerramento de sessão
**Resultado:** [resumo do que foi feito, decisões tomadas, conhecimentos gerados]
```

Se houve INGEST, QUERY ou LINT nesta sessão, registre cada um separadamente.

Se novo conhecimento foi gerado (conceitos, entidades, análises), crie as páginas
correspondentes em `wiki/concepts/`, `wiki/entities/` ou `wiki/analysis/`
e atualize os stats em `wiki/index.md`.

### Etapa 4: Sincronizar externamente

Leia `local-config.yaml`. Se `external_sync.enabled: true`:

**Determine o provider e use a ferramenta MCP correspondente:**

| Provider | Ferramenta MCP |
|----------|----------------|
| `notion` | `Notion:notion-update-page` |
| `trello` | Use API via bash com `SYNC_API_KEY` do `.env` |
| `google_drive` | `GoogleDrive:google_drive_create_file` |
| `airtable` | Use API via bash com `SYNC_API_KEY` do `.env` |
| `linear` | Use API via bash com `SYNC_API_KEY` do `.env` |
| `github_projects` | Use `gh` CLI |

Para detalhes de payload por provider, consulte [providers.md](providers.md).

**Se o sync falhar:** salve apenas localmente, alerte o usuário e continue. Não interrompa o trabalho.

### Etapa 5: Confirmar

Exiba ao usuário:

```
✅ Sessão registrada.

📍 Último passo: [o que foi feito]
➡️  Próximo passo: [próxima ação concreta]
📚 Wiki: [se houve atualizações — quais páginas]
💾 Sync: [provider configurado] / Apenas local

Para retomar: leia docs/PROJECT_STATUS.md.
```

## Exemplos

**Entrada:** usuário diz "encerrando por hoje, implementei o login com JWT"
**Saída esperada em PROJECT_STATUS.md:**
```
### ✅ Último passo concluído
Implementado fluxo de autenticação com JWT, incluindo geração e validação de token.

### ➡️ Próximo passo
Criar middleware de autorização por role e proteger rotas privadas.
```
**Saída esperada em wiki/log.md:**
```
## 2026-06-14 — SESSION
**Operação:** Encerramento de sessão
**Resultado:** Implementado JWT auth. Decisão: usar HS256 por simplicidade inicial.
```

**Entrada:** usuário diz "*sync-status" sem mencionar o que fez
**Comportamento:** perguntar "O que foi concluído nesta sessão?" antes de atualizar.

**Entrada:** usuário diz "*wiki ingest" com arquivo em raw/sources/
**Comportamento:** executar fluxo INGEST descrito no CLAUDE.md, registrar em wiki/log.md.
