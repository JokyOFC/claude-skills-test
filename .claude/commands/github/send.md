---
description: Esteira de envio — commita, faz push da branch atual e abre/mergeia PR para a dev (com checagem de conflitos e CI).
model: sonnet
allowed-tools: Bash(git:*), Bash(gh:*), Read, Grep
---

# /github:send — Enviar branch atual para a `dev`

Execute a esteira **sequencial** abaixo. Em **qualquer falha ou ambiguidade,
PARE e consulte o usuário** antes de prosseguir. Nunca pule etapas.

## 1. Validar branch atual

- Descubra a branch atual: `git branch --show-current`.
- Se for `dev` ou `main`, **aborte imediatamente** com o aviso:
  "Você está na branch `<branch>`. O /github:send só roda a partir de uma branch
  de trabalho (feature/fix/chore/...). Faça checkout em uma branch de tarefa
  antes de continuar."

## 2. Commitar alterações pendentes

- Verifique pendências com `git status --porcelain`.
- Se houver mudanças não commitadas, agrupe por bloco lógico e commite seguindo
  **Conventional Commits**, mensagens em **português no imperativo**
  (ex.: `feat: adicionar endpoint de login`).
- Se não houver nada pendente e a branch também não tiver commits à frente da
  `dev`, avise o usuário e pare (não há o que enviar).

## 3. Push da branch

- `git push -u origin <branch-atual>`.
- Em caso de erro (ex.: rejeição por divergência), pare e reporte ao usuário.

## 4. Criar Pull Request para a `dev`

- Antes de criar, verifique se já existe PR aberta desta branch:
  `gh pr list --head <branch-atual> --state open --json number,url`.
- Se **já existir**, reutilize-a (não crie outra).
- Se **não existir**, crie:
  `gh pr create --base dev --head <branch-atual> --title "<título em Conventional Commits>" --body "<descrição técnica>"`.
  - Título: resumo no padrão Conventional Commits.
  - Corpo: descrição técnica do que foi feito, motivação e pontos de atenção.

## 5. Checar conflitos

- Rode `gh pr view <branch-atual> --json mergeable,mergeStateStatus`.
- Se `mergeable` for **`CONFLICTING`**, **pergunte ao usuário**:
  > "Detectei conflitos de merge com a dev. Deseja que eu analise e tente
  > resolver automaticamente, ou prefere resolver manualmente?"
  - **Resolução automática** (se o usuário aceitar):
    1. `git fetch origin dev`
    2. `git merge origin/dev` na branch atual
    3. Resolver os marcadores de conflito **analisando o contexto** de cada lado
       (não escolher cegamente uma versão).
    4. `git add` nos arquivos resolvidos e commite o merge.
    5. `git push`.
    6. Volte ao passo 5 para reconfirmar que não há mais conflitos.
  - **Resolução manual**: pare e devolva o controle ao usuário.
- Se `mergeable` for **`UNKNOWN`**, o GitHub ainda está calculando: aguarde
  alguns segundos e **cheque novamente** antes de decidir.

## 6. Aguardar checks de CI

- Se a PR tiver checks de CI, aguarde com `gh pr checks <branch-atual> --watch`.
- Se **algum check falhar**, **pare e pergunte ao usuário** como proceder
  (mostre quais checks falharam).

## 7. Mergear e sincronizar

- `gh pr merge <branch-atual> --merge --delete-branch`.
- Depois: `git checkout dev && git pull origin dev`.
- Confirme ao usuário o resultado: PR mergeada, branch deletada e `dev`
  atualizada localmente.
