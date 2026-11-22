# AGENTS.md

digest-telegram is a planning document, not code. It describes the plan to consolidate 21 digest and radar agents into the `juninmd/digest-kit` engine, and it has no bot, no build and no tests.

## Stack

- Markdown and PNG images only

## Commands

- None. There is no build, test or lint step and no CI workflow in this repository.

## Structure

- `README.md`: the whole plan (context, tier inventory, per-agent destinations)
- `docs/readme/`: README images (`hero.png`, `architecture.png`, `digest.png`)

## Conventions

- Keep the tier table and the inventory table in sync. Both list the same 21 repositories.
- Destinations use the `digest-kit` terms: `topics/<name>.ts` or `providers/<name>.ts`.
- The README is in pt-BR.

## Gotchas

- Facts here describe other repositories. Check the source repo before you change a row.
- Do not add code or a bot here without a decision from the owner. The plan says this repo is documentation only.

<!-- base-actions-main-policy -->
## GitHub Actions — padrão obrigatório (@main)

- Referencie as actions internas sempre pelo branch, nunca por SHA:
  - `uses: juninmd/base-actions/<action>@main`
  - `uses: juninmd/base-actions/.github/workflows/<arquivo>.yml@main`
- Não fixe `@<sha>` em `juninmd/base-actions`: a atualização é centralizada no
  `main` e passa a valer no próximo run.
- PRs cujos checks não iniciam por billing/spending limit são mesclados pelo bot central
  `juninmd/base-actions/.github/workflows/auto-merge-billing-blocked.yml`.
  Para impedir o merge automático de um PR, aplique a label `do-not-merge`.
