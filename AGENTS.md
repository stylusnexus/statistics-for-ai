# AGENTS.md

Workspace-wide rules (commits, security, plan-before-code, approval gates) live in `/Applications/Development/Projects/AGENTS.md`; this file only adds what is specific to this project.

## Project Overview

A ten-lesson, self-paced course on the statistics needed for AI, GenAI, and LLM work, licensed CC BY 4.0 (see `LICENSE`). Written with AI assistance and human-reviewed; `README.md` calls it a study aid, not a peer-reviewed textbook.

## Development Commands

None. There is no build step or package manifest. Open `index.html` in a browser.

## Architecture

- `index.html`: course landing page.
- `ai-statistics-foundations/lsn-*.html`: the 10 lessons. Each is self-contained (inline CSS and inline SVG, no external scripts, styles, fonts, or images).
- `ai-statistics-foundations/meta.json`: EngramQuest course metadata (progress flags ship as `false`).
- `ai-statistics-foundations/review/`: flashcards (`question :: answer`, one per line) and a hints JSON.
- `ai-statistics-foundations/ai-statistics-foundations-memory.canvas`: Obsidian JSON Canvas memory map.

## Common Gotchas

- Keep lessons self-contained: no external assets.
- The folder name `ai-statistics-foundations` is the course slug the Obsidian plugin expects; do not rename it.
- Canvas file-node links are vault-relative and only resolve after import into a vault.
- Do not copy third-party prose, figures, or datasets; the README's license statement depends on all material being original.
