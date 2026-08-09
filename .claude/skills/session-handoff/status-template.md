# Template de Status

## Conteúdo
- [Campos obrigatórios](#campos-obrigat%C3%B3rios)
- [Regras de preenchimento](#regras-de-preenchimento)

---

## Campos obrigatórios

```markdown
# STATUS DO PROJETO

## Identificação
- **Nome:** [CATEGORIA] - [Nome do Projeto]
- **Status:** [Não iniciado | Em andamento | Em revisão | Concluído]
- **Prioridade:** [Alta | Média | Baixa]
- **Data de início:** DD/MM/YYYY
- **Última atualização:** DD/MM/YYYY

## Stack
[tecnologias separadas por vírgula]

## Repositório
- **GitHub:** [URL ou "Não criado ainda"]
- **Branch atual:** [main | feat/nome]
- **Link externo:** [URL em produção ou "N/A"]

---

## Sessão atual

### ✅ Último passo concluído
[Passado. Concreto. Verificável. Ex: "Implementado fluxo de login com JWT."]

### ➡️ Próximo passo
[Imperativo. Acionável sem contexto extra. Ex: "Criar middleware de autorização por role."]

### 🚧 Bloqueios
[Lista de bloqueios ou "Nenhum"]

---

## Decisões técnicas
[Uma por linha. Ex: "Optado por Supabase pela integração nativa com PostgreSQL."]

---

## Histórico

| Data | Resumo | Próximo passo |
|------|--------|---------------|
| DD/MM/YYYY | [resumo] | [próximo passo] |
```

---

## Regras de preenchimento

- **Último passo:** sempre no passado ("foi feito X"), nunca no futuro
- **Próximo passo:** deve ser executável por qualquer pessoa sem contexto adicional
- **Histórico:** apenas adicionar linhas, nunca remover — é o log do projeto
- **Status:** reflete o estado real agora, não o planejado
