# CLAUDE.md

Este arquivo orienta o Claude Code ao trabalhar neste repositório.

<gitflow_workflow>

Estas regras são **estritas e proativas**. Devem ser seguidas automaticamente, sem
precisar que o usuário relembre, em toda e qualquer tarefa que envolva código.

## 1. Branch de trabalho (sempre antes de criar/alterar código)

- **Antes de criar ou alterar qualquer código**, verificar a branch atual com
  `git branch --show-current`.
- Se a branch atual **não** for uma branch de trabalho relacionada à tarefa em
  andamento, **não** trabalhar nela. Em vez disso:
  1. `git checkout dev && git pull`
  2. Criar uma branch nova a partir da `dev`, com a semântica correta conforme o
     tipo da tarefa:
     - `feature/<nome>` — nova funcionalidade
     - `fix/<nome>` — correção de bug
     - `chore/<nome>` — manutenção, configs, dependências
     - `refactor/<nome>` — refatoração sem mudança de comportamento
     - `docs/<nome>` — documentação
     - `test/<nome>` — testes
     - `ci/<nome>` — pipelines / integração contínua
- `<nome>` sempre em **kebab-case** (ex.: `feature/login-com-google`).

## 2. Commits

- Fazer commits **automáticos por bloco lógico concluído** (não acumular várias
  mudanças não relacionadas em um único commit).
- Seguir **estritamente** o padrão **Conventional Commits**:
  `feat:`, `fix:`, `chore:`, `refactor:`, `docs:`, `test:`, `ci:`, etc.
- Mensagens **em português, no modo imperativo**
  (ex.: `feat: adicionar validação de e-mail no cadastro`).

## 3. Proibições (nunca fazer)

- **Proibido commitar direto** nas branches `dev` ou `main`.
- **Proibido fazer push** sem o usuário acionar explicitamente o comando
  `/github:send`.
- **Proibido mergear fora do fluxo de Pull Request** (todo merge passa por PR).

</gitflow_workflow>
