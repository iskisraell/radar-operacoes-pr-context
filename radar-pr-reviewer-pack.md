# Radar Operações MVP - PR body

## Suggested title

`feat(radar): integrate Radar MVP into Operações`

## Body

```markdown
## Contexto

Este PR integra o Radar como módulo nativo do Operações, usando auth, layout, políticas, Blade, Vue/Vuex e fluxos existentes.

## Entrega

- Adiciona Radar em `/radar` com Equipamentos, Dashboard, Mapa e Detalhe.
- Separa os contratos de dados por endpoint: lista, mapa, detalhe, dashboard e filtros.
- Mantem CSP/permissões como fronteira forte: filtros reduzem escopo, nunca ampliam.
- Adiciona cache operacional por concessão com clear, warm e health.
- Concede acesso aos perfis de analista de concessão e remove Radar de perfis não analistas.
- Registra evidência final em `docs/radar-final-validation-evidence.md`.

## Validação

- `./sail php vendor/bin/phpunit tests/Feature/Radar/`
  - OK: 119 tests, 5604 assertions.
- `./sail artisan route:list --path=radar`
  - OK: 10 rotas Radar autenticadas.
- Cache validado para `sp`, `pr` e `ba` com:
  - `radar:cache:clear`
  - `radar:cache:warm --payload=all`
  - `radar:health`

## Documento visual

Anexar no PR: `radar-pr-story-artifact.html`

O documento visual cobre a história da implementação, decisões técnicas, superfícies entregues, evidências, mídias e perguntas frequentes para revisão.

## Observações

- `CACHE_DRIVER=file` ainda aparece na evidência local; confirmar backend real de cache em homolog/produção antes do deploy se houver múltiplos app servers.
- Bundles gerados devem entrar somente se o processo de deploy exigir artefatos finais versionados.
```

## Media already wired into the HTML artifact

- `pr-media/01-radar-menu-and-home.png`
- `pr-media/02-equipment-filters-list.png`
- `pr-media/03-detail-action-state.png`
- `pr-media/04-dashboard-gcharts.png`
- `pr-media/05-map-clustering.png`
- `pr-media/06-cache-clear-warm-health.png`
- `pr-media/07-analyst-profile-walkthrough.mp4`
- `pr-media/08-non-analyst-denied.png`
