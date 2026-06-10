# claude-skills-test

Projeto de teste com fluxo de Gitflow automatizado via Claude Code.

## Fluxo de trabalho

Toda tarefa começa a partir da `dev`:

1. `git checkout dev && git pull`
2. `git checkout -b feature/<nome>` (ou `fix/`, `chore/`, `docs/`, etc.)
3. Desenvolver e commitar em blocos lógicos (Conventional Commits em português)
4. Acionar `/github:send` para abrir o PR para a `dev`

## Comandos disponíveis

- `/github:send` — envia a branch atual e abre PR para a `dev`
- `/github:uptodate` — promove a `dev` para a `main` (release)
- `/github:init` — inicializa o projeto no GitHub (idempotente)

## Branches

- `main` — produção (permanente)
- `dev` — integração (permanente, nunca deletada)
