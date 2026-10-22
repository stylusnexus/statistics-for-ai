# AGENTS.md

Workspace-wide rules (commits, security, plan-before-code, approval gates) live in the `AGENTS.md` at the root of the Projects workspace folder. This file adds only what is specific to this project.

## Project Overview

Two self-paced courses, licensed CC BY 4.0 (see `LICENSE`). One is a nineteen-lesson course on the statistics needed for AI, GenAI, and LLM work. The other is a seven-lesson companion on the linear algebra and calculus underneath it. Written with AI assistance. Humans reviewed lessons 1–10. AI reviewers alone checked the later statistics lessons and the math course. `README.md` calls the material a study aid, not a peer-reviewed textbook.

## Development Commands

None. There is no build step or package manifest. Open `index.html` in a browser.

## Architecture

- `index.html`: statistics course landing page. `math.html`: math course landing page.
- `ai-statistics-foundations/lsn-*.html`: the 19 statistics lessons (1–10 original, 11–19 added for the probability and inference fundamentals).
- `ai-math-foundations/`: the math course, with the same layout (`lsn-*.html`, `meta.json`, `review/`, memory-map canvas). Its folder name is also a plugin course slug; do not rename it.
- Lesson files in both folders: Each is self-contained (inline CSS and inline SVG, no external scripts, styles, fonts, or images).
- `ai-statistics-foundations/meta.json`: EngramQuest course metadata (progress flags ship as `false`).
- `ai-statistics-foundations/review/`: flashcards (`question :: answer`, one per line) and a hints JSON.
- `ai-statistics-foundations/ai-statistics-foundations-memory.canvas`: Obsidian JSON Canvas memory map.

## Common Gotchas

- Keep lessons self-contained: no external assets.
- The folder name `ai-statistics-foundations` is the course slug the Obsidian plugin expects; do not rename it.
- Canvas file-node links are vault-relative and only resolve after import into a vault.
- Do not copy third-party prose, figures, or datasets; the README's license statement depends on all material being original.
