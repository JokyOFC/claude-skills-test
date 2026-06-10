---
description: Esteira de release — abre/mergeia PR da dev para a main (com checagem de conflitos e CI). A dev é permanente e nunca é deletada.
model: sonnet
allowed-tools: Bash(git:*), Bash(gh:*), Read, Grep
---

# /github:uptodate — Promover `dev` → `main` (release)

Execute a esteira **sequencial** abaixo. Em **qualquer falha ou ambiguidade,
PARE e consulte o usuário**. Nunca pule etapas.

## 1. Atualizar e checar se há o que promover

- `git checkout dev && git pull origin dev`
- `git fetch origin main`
- Verifique se a `dev` tem commits novos em relação à `main`:
  `git log origin/main..origin/dev --oneline`.
- Se **não houver** commits novos, avise o usuário ("A `dev` já está sincronizada
  com a `main`, não há nada para promover.") e **encerre**.

## 2. Criar Pull Request da `dev` para a `main`

- Antes de criar, verifique se já existe PR aberta `dev → main`:
  `gh pr list --base main --head dev --state open --json number,url`.
- Se **já existir**, reutilize-a (não crie outra).
- Se **não existir**, crie:
  `gh pr create --base main --head dev --title "release: <resumo>" --body "<corpo>"`.
  - O **corpo** deve ser gerado a partir de `git log origin/main..origin/dev --oneline`,
    **agrupado por tipo** de Conventional Commit (ex.: seções
    `### Features`, `### Fixes`, `### Chores`, `### Docs`, etc.).

## 3. Checar conflitos

- Rode `gh pr view dev --json mergeable,mergeStateStatus`.
- Se `mergeable` for **`CONFLICTING`**, **pergunte ao usuário**:
  > "Detectei conflitos de merge com a main. Deseja que eu analise e tente
  > resolver automaticamente, ou prefere resolver manualmente?"
  - **Resolução automática** (se aceitar): resolva em uma **branch temporária a
    partir da dev** — **nunca commitando direto na main**:
    1. `git checkout dev && git pull origin dev`
    2. `git checkout -b chore/resolve-release-conflicts`
    3. `git fetch origin main && git merge origin/main`
    4. Resolver os marcadores de conflito **analisando o contexto** de cada lado.
    5. `git add` + commit do merge + `git push -u origin chore/resolve-release-conflicts`.
    6. Abra PR dessa branch temporária **para a dev**, mergeie, atualize a dev e
       **recomece esta esteira** do passo 1.
  - **Resolução manual**: pare e devolva o controle ao usuário.
- Se `mergeable` for **`UNKNOWN`**, aguarde e **cheque novamente**.

## 4. Aguardar checks de CI

- Se a PR tiver checks de CI, aguarde com `gh pr checks dev --watch`.
- Se algum check falhar, **pare e pergunte ao usuário**.

## 5. Mergear (preservando a dev)

- `gh pr merge dev --merge` — **SEM** a flag `--delete-branch`.
  > A `dev` é **permanente** e **jamais** pode ser deletada.
- Depois: `git checkout dev && git pull origin dev`.
- Confirme ao usuário: release mergeada na `main`, `dev` preservada e atualizada.
