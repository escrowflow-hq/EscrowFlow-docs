# Contributing to EscrowFlow Docs

Thanks for helping improve the EscrowFlow documentation. This repo is markdown-only — no build step, no dependencies to install.

## How to contribute

1. Fork the repo, create a branch:
   ```bash
   git checkout -b docs/topic-name
   ```
2. Write docs in Markdown (or update existing files).
3. Follow [Conventional Commits](https://www.conventionalcommits.org/) for your commit messages:
   - `docs: add security section`
   - `docs: fix typo in API guide`
   - `docs: update roadmap for Q4 2026`
4. Submit a PR describing what changed and why.
5. A maintainer reviews and merges.

## Adding a new doc

- Architecture docs live in `docs/architecture/`
- How-to guides live in `docs/guides/`
- Standalone pages (`team.md`, `roadmap.md`) live in `docs/`
- Link new pages from the **Docs Navigation** table in the root `README.md` so they're discoverable.

## Style guide

- Use Markdown headings (`#`, `##`, `###`) — one `#` title per page.
- Fence code blocks with a language tag (` ```bash `, ` ```typescript `, ` ```rust `, etc.).
- Link to the live demo, block explorers, and GitHub repos where relevant instead of restating details that live there.
- Use ASCII diagrams for architecture and data-flow illustrations.
- Use tables for quick-reference material (endpoints, env vars, error codes).
- Prefer short, direct sentences over marketing language.
