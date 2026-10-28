# maieutic

herdr plugin + Claude Code learning harness. AGPL-3.0-only, adapted from LearnHarness.

## Commands

None yet. The project is in the design phase; the Phase 0 spikes in `docs/roadmap.md` come first.

## Architecture

- Planned staged hybrid (design paper §6):
  - an MCP server enforces the lesson rules and owns the session event log;
  - an interactive `claude` tutor runs in a herdr pane, next to a pane that shows quiz cards;
  - headless `claude -p` workers do knowledge-graph extraction and research.
- Upstream reference checkout: `~/Projects/mouseion/learnharness` (fork `Mourey/learnharness`), baseline commit `3dc8847`.
- Plugin conventions to mirror: `~/Projects/mouseion/dotfiles/shared/herdr-plugins/artifacts/`. That means a manifest plus bash entrypoints, with keys bound in the user's `config.toml` rather than in the manifest.
- Design paper: Obsidian wiki, `raw/documents/2026-10-28-learnharness-adaptation-paper.md`; summary in `wiki/sources/`.

## Conventions

- Every file adapted from LearnHarness starts with a header naming the upstream path and commit; see `NOTICE`. AGPL requires this attribution, and the header is how we track drift from upstream.
- Every model call goes through the official `claude` binary on the user's subscription. Don't extract Claude OAuth or setup tokens to drive another engine: the subscription is for Anthropic's own clients.

## Watch out for

- Never pass `--bare`. On this install it reads only `ANTHROPIC_API_KEY`, so it would silently switch to API billing or fail. For the same reason, strip `ANTHROPIC_API_KEY` from the environment of child processes.
- LearnHarness's `docs/architecture.md` no longer matches its source. Cite source `file:line`, not the docs.
- herdr and claude flags marked `[A]` or `[M]` in the paper are unverified. Check them against `--help` on the installed versions (herdr 0.9.5, claude 2.1.312) before relying on them.
- Don't build past an unresolved spike. S1 decides the launcher shape, and S2 decides whether the pane can keep quiz answers hidden.
