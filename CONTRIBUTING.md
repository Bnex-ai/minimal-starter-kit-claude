# Contribuindo com este repositório

Este documento descreve como propor, revisar e publicar uma mudança neste
repositório — seja você quem mantém o projeto ou alguém de fora sugerindo
uma melhoria.

---

## 1. Antes de mexer em código: Issue

Se a mudança ainda é uma ideia, um problema encontrado ou algo que precisa
de discussão antes de virar código, abra uma **Issue** em vez de já
escrever a alteração. Uma Issue registra a sugestão, permite discutir o
escopo e evita trabalho jogado fora por falta de alinhamento prévio.

Só pule direto para o Pull Request quando a mudança for pequena e óbvia
(typo, correção de link, ajuste de exemplo).

---

## 2. Propondo a mudança: Pull Request (PR)

Um **Pull Request** é a proposta formal de incorporar uma mudança na
branch principal (`main`). É sempre feito em uma branch separada, nunca
diretamente na `main`.

**Quem não tem permissão de escrita no repositório:**
1. Faça um **Fork** — uma cópia deste repositório na sua própria conta.
2. Crie uma branch no fork: `git checkout -b nome-da-mudanca`.
3. Faça as alterações e o(s) commit(s).
4. Suba a branch para o seu fork e abra o Pull Request contra este
   repositório (que passa a ser o `upstream` do seu fork).

**Quem tem permissão de escrita (colaborador convidado):**
1. Crie a branch direto neste repositório: `git checkout -b nome-da-mudanca`.
2. Faça as alterações e o(s) commit(s).
3. Suba a branch (`git push origin nome-da-mudanca`) e abra o PR.

### Boas práticas no PR

- Descreva **o quê** mudou e **por quê** — o diff já mostra o *o quê* em
  detalhe; a descrição existe para explicar o motivo.
- Referencie a Issue relacionada, se houver (`Closes #12`).
- Um PR por assunto. PRs que misturam mudanças não relacionadas são mais
  difíceis de revisar e de reverter se algo der errado.
- Nunca inclua chaves, tokens, credenciais ou dados sensíveis no diff —
  nem em código, nem em exemplo, nem em mensagem de commit. Se algo desse
  tipo for commitado por engano, avise antes de abrir o PR: remover do
  histórico depois de público exige reescrever a branch inteira.

---

## 3. Revisando: o que checar antes de aprovar

Quem mantém o repositório revisa o PR pela aba **Files changed** — o
diff é a prova do que muda; descrição em prosa não é verificável.

Checklist mínimo antes de aprovar:

| Checar | Por quê |
|---|---|
| O diff faz só o que a descrição promete | Evita mudança escondida dentro de um PR aparentemente inofensivo |
| Nenhum segredo, chave ou dado sensível entrou no diff | Uma vez público, rotacionar a credencial é a única solução real |
| A mudança segue as convenções já estabelecidas no projeto (ver `.claude/CLAUDE.md`) | Consistência facilita a próxima sessão/pessoa a entender o repositório |
| Os checks automáticos (se configurados) estão verdes | Falha de CI não deve ser ignorada só porque "parece que funciona" |
| A mudança é reversível ou tem plano de rollback claro | Todo merge pode precisar ser desfeito |

O veredito da revisão é um destes três:
- **Approve** — aprova, libera para merge.
- **Request changes** — bloqueia o merge até o ponto levantado ser resolvido.
- **Comment** — observação que não bloqueia, para contexto ou sugestão opcional.

---

## 4. Publicando: Merge

Com o PR aprovado e os checks (se houver) verdes, o **Merge** incorpora
a branch na `main`. Não existe um passo separado de "publicar" — o
merge já é a publicação; o repositório reflete a mudança imediatamente.

Formas de merge:

| Modo | Quando usar |
|---|---|
| **Squash and merge** | Padrão recomendado aqui — condensa todos os commits do PR em um só, mantendo o histórico da `main` limpo e um commit por mudança lógica |
| **Merge commit** | Quando o histórico detalhado da branch tem valor próprio (ex.: PR grande, com etapas relevantes de decisão) |
| **Rebase and merge** | Quando se quer manter os commits individuais mas sem um commit de merge extra |

Depois do merge, apague a branch (o próprio GitHub oferece o botão) —
branches mescladas não precisam continuar existindo.

---

## 5. Proteção da `main`

Recomenda-se ativar **branch protection** na `main`:

- Exigir Pull Request para qualquer mudança (bloqueia push direto).
- Exigir pelo menos 1 aprovação antes do merge.
- Exigir que os checks automáticos passem antes do merge, se configurados.

Isso vale mesmo em projeto de mantenedor único: a branch protection
funciona como uma segunda checagem contra erro — o PR mostra o diff
completo antes de ele virar permanente, em vez de a mudança já entrar
direto na `main`. Se quem mantém o repositório também precisar
push direto ocasionalmente, o GitHub permite marcar exceção de
administrador nas regras da branch — decisão a ser tomada
conscientemente, não um padrão a ignorar.

---

## Glossário rápido

| Termo | Significado |
|---|---|
| Issue | Registro de problema, ideia ou tarefa — sem diff ainda |
| Pull Request (PR) | Proposta formal de incorporar uma mudança, com diff |
| Fork | Cópia do repositório em outra conta, vinculada ao original |
| Upstream | O repositório original ao qual um fork está ligado |
| Review | Avaliação de um PR (Approve / Request changes / Comment) |
| Merge | Incorporação da mudança na branch principal |
| Branch protection | Regras que restringem alterações diretas na branch principal |

Para o vocabulário completo e o passo a passo de publicação segura no
GitHub, ver [`docs/reference/workshop-github.md`](docs/reference/workshop-github.md).
