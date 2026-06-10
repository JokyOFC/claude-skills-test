---
description: Inicializa o projeto no GitHub do zero (idempotente) — git init, .gitignore, commit inicial, cria repo, branches main/dev e a configuração do Gitflow.
argument-hint: [nome-do-repo] [--public]
model: sonnet
allowed-tools: Bash(git:*), Bash(gh:*), Read, Write, Grep
---

# /github:init — Inicializar projeto no GitHub

Comando **idempotente**: cada passo só executa se ainda não tiver sido feito.
Em qualquer falha ou ambiguidade, **pare e consulte o usuário**.

Argumentos recebidos: `$ARGUMENTS`

## 1. Nome e visibilidade do repositório

- O **primeiro argumento** é o nome do repositório.
- Se ausente, **pergunte ao usuário**, sugerindo o nome da pasta atual
  convertido para **kebab-case** (`basename "$PWD"` normalizado).
- Visibilidade: **privado por padrão**. Só será **público** se o argumento
  `--public` estiver presente.

## 2. Remoto já existente?

- Verifique: `git remote get-url origin` (ou `git remote -v`).
- Se **já existir** `origin`, **pare e pergunte**: "Já existe um remoto `origin`
  configurado (`<url>`). Deseja apenas completar a estrutura faltante
  (branches/config) ou abortar?"

## 3. Git local, .gitignore, segurança e commit inicial

- Se não houver repositório git (`git rev-parse --git-dir` falha), rode
  `git init -b main`.
- Se **não existir** `.gitignore`, **detecte a stack** (ex.: `package.json` →
  Node; `pyproject.toml`/`requirements.txt` → Python; `go.mod` → Go; etc.) e crie
  um `.gitignore` adequado.
- **Antes de `git add .`**, verifique que **não há segredos** fora do `.gitignore`
  (procure por `.env`, `.env.*`, chaves `*.pem`/`id_rsa`/`*.key`, credenciais).
  Se encontrar algo sensível rastreável, **pare e avise o usuário**.
- `git add .` e commit inicial: `chore: commit inicial do projeto`
  (só se houver algo a commitar).

## 4. Criar repositório remoto e push

- Crie o repo e conecte o remoto:
  `gh repo create <nome> --private --source=. --remote=origin`
  (use `--public` no lugar de `--private` se o usuário pediu público).
- `git push -u origin main`.
- Se o repositório **já existir na conta** do usuário, **pergunte**: "O repositório
  `<nome>` já existe na sua conta. Deseja conectar a ele ou escolher outro nome?"

## 5. Branch `dev`

- Se a branch `dev` não existir, crie a partir da `main`:
  `git checkout -b dev` e publique: `git push -u origin dev`.
- Termine com **checkout na `dev`**.

## 6. Ajustes opcionais do repositório (perguntar)

- **Pergunte (opcional)** se deseja definir a `dev` como branch padrão:
  `gh repo edit --default-branch dev`.
- **Pergunte (opcional)** se deseja habilitar o auto-delete de branches
  mergeadas: `gh repo edit --delete-branch-on-merge`.

## 7. Garantir a configuração do Gitflow

- Garanta que existem (criando se faltarem):
  - `CLAUDE.md` com a seção `<gitflow_workflow>`
  - `.claude/commands/github/send.md`
  - `.claude/commands/github/uptodate.md`
- Commite a configuração **na `dev`** (seguindo Conventional Commits, ex.:
  `chore: configurar fluxo de gitflow automatizado`) e faça push.
- **Relatório final** ao usuário, contendo:
  - URL do repositório
  - Visibilidade (privado/público)
  - Branches existentes (`main`, `dev`)
  - Branch padrão
  - Comandos disponíveis (`/github:send`, `/github:uptodate`, `/github:init`)
