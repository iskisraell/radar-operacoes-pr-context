# Radar > Operações — plano seguro para merge com `main`

## Contexto reunido

- Branch de trabalho: `feature/radar-operacoes-mvp`.
- `main` atual verificado previamente: `3d2b962a7` (`hotfix/posting-week-report`).
- Dry merge isolado encontrou conflitos em:
  - `.gitignore`
  - `public/css/app.css`
  - `public/js/app.js`
  - `public/mix-manifest.json`
- Código-fonte do Radar não apresentou conflito direto no dry merge.
- Laravel 8 / Laravel Mix: assets versionados devem ser gerados por build, não editados manualmente.

## Estado atual

- Radar tem validação local: 119 testes / 5604 asserções.
- `./sail artisan route:list --path=radar` lista 10 rotas autenticadas.
- `/docs` está ignorado para manter artefatos e mídias fora do PR.
- Bundles gerados podem ficar sujos após build; só devem ser incluídos se o processo de deploy exigir.

## Regras de negócio

- Escopo do Radar é uma interseção: configuração de concessões + permissões/filtros do usuário + filtros da tela.
- Filtros da tela só estreitam resultado.
- Ações do detalhe dependem de policy; usuário sem permissão não recebe URL de ação.

## Riscos

- Resolver `public/js/app.js`, `public/css/app.css` ou `public/mix-manifest.json` manualmente pode gerar bundle incoerente.
- `main` trouxe mudanças grandes fora de Radar; não misturar refatorações de relatórios/logs com o PR Radar.
- Se produção usar múltiplos app servers, confirmar cache compartilhado antes do deploy.

## Fase 1 — sincronização controlada

1. Rodar `git status --short --branch`.
2. Garantir que só existam mudanças esperadas ou pedir decisão antes de prosseguir.
3. Atualizar `main`: `git fetch origin main`.
4. Conferir `git log --oneline HEAD..origin/main`.
5. Fazer merge de `origin/main` na branch apenas com autorização explícita.

## Fase 2 — resolver conflitos

1. `.gitignore`: manter `/.claude`, `/AGENTS.md` e `/docs`.
2. Bundles: não resolver escolhendo `ours` ou `theirs` como solução final.
3. Se bundles não entram no PR: restaurar/remover conflitos de `public/*` do índice e deixar o deploy compilar.
4. Se bundles entram no PR: resolver fontes primeiro, rodar `./sail npm run production` e aceitar somente o resultado do build.

## Fase 3 — validação

1. `./sail php vendor/bin/phpunit tests/Feature/Radar/`
2. `./sail artisan route:list --path=radar`
3. `./sail npm run production` se frontend source mudou ou se bundles forem exigidos.
4. `git diff --check`
5. Confirmar que `/docs` segue ignorado e que nenhuma mídia entrou no índice.

## Fase 4 — PR

1. Criar commit de merge somente depois dos testes.
2. Push somente após aprovação.
3. Abrir PR com corpo curto em PT-BR e anexar `radar-pr-story-artifact.html` como documentação visual.
4. Registrar handoff no Obsidian com status, comandos e link do PR.

## Prompt agent-ready

```text
Você está em `feature/radar-operacoes-mvp` no repo Operações.

Objetivo: sincronizar com `main`, resolver conflitos sem dívida técnica e preparar a branch para PR.

Antes de tocar arquivos:
- Rode `git status --short --branch`.
- Rode `git fetch origin main`.
- Rode `git log --oneline HEAD..origin/main`.
- Não faça stash, reset, push, force push ou troca de branch sem autorização explícita.

Contexto:
- Dry merge anterior encontrou conflitos diretos em `.gitignore`, `public/css/app.css`, `public/js/app.js`, `public/mix-manifest.json`.
- Código-fonte do Radar não apresentou conflito direto.
- `/docs` deve continuar ignorado.
- Bundles gerados só entram se o processo de deploy exigir.

Resolução:
- Em `.gitignore`, manter `/.claude`, `/AGENTS.md` e `/docs`.
- Não editar bundles gerados manualmente.
- Se bundles forem excluídos: manter fonte como verdade e não commitar `public/*`.
- Se bundles forem incluídos: resolver fontes, rodar `./sail npm run production` e commitar apenas o output gerado pelo build.

Validação obrigatória:
- `./sail php vendor/bin/phpunit tests/Feature/Radar/`
- `./sail artisan route:list --path=radar`
- `./sail npm run production` se houver frontend source alterado ou bundle exigido.
- `git diff --check`
- `git status --short`

Entrega:
- Relate conflitos resolvidos.
- Relate se bundles foram incluídos ou excluídos.
- Prepare o PR com corpo curto em PT-BR e referência ao HTML visual.
- Não abra PR nem faça push sem instrução explícita.
```

