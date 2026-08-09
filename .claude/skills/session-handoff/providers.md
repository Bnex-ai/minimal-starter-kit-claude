# Providers de Sync Externo

Referência carregada pela skill apenas quando `external_sync.enabled: true`.

## Conteúdo
- [Notion](#notion)
- [Trello](#trello)
- [Google Drive](#google-drive)
- [Airtable](#airtable)
- [Linear](#linear)
- [GitHub Projects](#github-projects)

---

## Notion

**Ferramenta MCP:** `Notion:notion-update-page`

Campos a enviar para a página de destino (`SYNC_DESTINATION_ID`):

```json
{
  "Status": "Em andamento",
  "Última atualização": "DD/MM/YYYY",
  "Última etapa concluída": "[texto do PROJECT_STATUS.md]",
  "Próxima etapa": "[texto do PROJECT_STATUS.md]",
  "Tecnologias": "[stack do projeto]"
}
```

Se a página não existir, use `Notion:notion-create-pages` com o mesmo payload.

---

## Trello

**Ferramenta:** API REST via bash. `SYNC_API_KEY` = Trello API Key. `SYNC_DESTINATION_ID` = Card ID.

```bash
curl -X PUT \
  "https://api.trello.com/1/cards/${SYNC_DESTINATION_ID}" \
  -d "key=${SYNC_API_KEY}&token=${TRELLO_TOKEN}&desc=[resumo da sessão]"
```

Adicione também um comentário com o resumo:

```bash
curl -X POST \
  "https://api.trello.com/1/cards/${SYNC_DESTINATION_ID}/actions/comments" \
  -d "key=${SYNC_API_KEY}&token=${TRELLO_TOKEN}&text=[último passo + próximo passo]"
```

---

## Google Drive

**Ferramenta MCP:** `GoogleDrive:google_drive_create_file` (sobrescreve se existir)

Salvar `PROJECT_STATUS.md` como arquivo de texto no `SYNC_DESTINATION_ID` (ID da pasta).

---

## Airtable

**Ferramenta:** API REST via bash. `SYNC_DESTINATION_ID` = Record ID.

```bash
curl -X PATCH \
  "https://api.airtable.com/v0/[BASE_ID]/[TABLE]/${SYNC_DESTINATION_ID}" \
  -H "Authorization: Bearer ${SYNC_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"fields": {"Status": "Em andamento", "Última Sessão": "[resumo]", "Próximo Passo": "[próximo]"}}'
```

---

## Linear

**Ferramenta:** API GraphQL via bash. `SYNC_DESTINATION_ID` = Issue ID.

```bash
curl -X POST https://api.linear.app/graphql \
  -H "Authorization: ${SYNC_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"query":"mutation { issueUpdate(id: \"'${SYNC_DESTINATION_ID}'\", input: {description: \"[resumo]\"}) { success } }"}'
```

---

## GitHub Projects

**Ferramenta:** `gh` CLI (requer autenticação prévia).

```bash
# Atualizar campo de status no project item
gh project item-edit \
  --project-id [PROJECT_ID] \
  --id ${SYNC_DESTINATION_ID} \
  --field-id [FIELD_ID] \
  --text "[último passo - próximo passo]"
```
