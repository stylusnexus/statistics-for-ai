# Statistics for AI, GenAI & LLMs

A self-paced, ten-lesson course covering the statistics you actually need to build, evaluate, and reason about AI systems, generative models, and LLMs.

Every example is drawn from AI and ML work — RAG chunk-size distributions, hallucination-detector base rates, latency percentiles, softmax temperature, cross-entropy loss — rather than the dice and urns most statistics material opens with. The lessons are deliberately sequential: each one assumes only what came before it, and no prior statistics background is required.

## Start here

Open **`index.html`** in any browser. That's the whole setup — no install, no build step, no dependencies.

Each lesson is a single self-contained HTML file with inline styles and inline SVG diagrams. There are no external scripts, stylesheets, fonts, or images, so the lessons render identically from a local file, a static host, or inside a sandboxed iframe.

## The lessons

| # | Lesson | Covers |
|---|--------|--------|
| 1 | Why Statistics Powers AI | Where statistics shows up across the AI stack — orientation, no math yet |
| 2 | Describing Data | Mean, variance, standard deviation, distribution shape, skew, outliers |
| 3 | Probability Foundations & Bayes' Theorem | Conditional probability, base rates, Bayesian updating |
| 4 | The Distributions That Run ML | Normal, Bernoulli, categorical/multinomial, Poisson |
| 5 | From Sample to Population | Sampling error, the Central Limit Theorem, confidence intervals, maximum likelihood |
| 6 | Correlation, Regression & Model Fit | Correlation vs. causation, linear regression, R², fitting as proto-training |
| 7 | Information Theory for AI | Entropy, cross-entropy, KL divergence |
| 8 | The Statistics Behind Training | Loss, gradient descent, over/underfitting, bias-variance, regularization |
| 9 | Evaluating AI Models Rigorously | Confusion matrices, precision/recall and their tradeoff, real win vs. noise |
| 10 | The Statistics Unique to LLMs | Perplexity, sampling and temperature, calibration, hallucination, RLHF |

Each lesson ends with a retry-friendly quiz.

## What else is in here

### Flashcards — `ai-statistics-foundations/review/`

Forty cards, four per lesson, in `Statistics for AI, GenAI & LLMs.md`. They're written for active recall — most pose a scenario and ask what breaks or what you'd do — rather than asking you to match a term to its definition.

The format is `question :: answer`, one card per line, which [Obsidian_to_Anki](https://github.com/Pseudonium/Obsidian_to_Anki) imports directly. Without that plugin it still reads fine as plain Markdown.

`Statistics for AI, GenAI & LLMs.json` holds three escalating hints per card (nudge → structural hint → keyword). It's consumed by the EngramQuest review UI and isn't especially useful standalone.

### Memory map — `ai-statistics-foundations-memory.canvas`

A 54-node [JSON Canvas](https://jsoncanvas.org/) laying out the whole course as one picture: one cluster per lesson, with edges showing how each concept feeds the next. It renders in Obsidian. The file-node links inside it are vault-relative, so they resolve once the course folder is imported (below) — not when browsing this repo directly.

## Using this inside Obsidian

The `ai-statistics-foundations/` folder is a drop-in EngramQuest Lesson Academy course. Copy the whole folder into your vault at:

```
<vault>/engram-quest/lessons/ai-statistics-foundations/
```

No renaming needed — the folder name is already the course slug the plugin expects. `meta.json` ships with all progress flags set to `false`, so it registers as a fresh, unstarted course.

To also wire up the review deck, copy the two files from `review/` into your vault's `engram-review/ai-cards/` and `engram-review/hints/` respectively, keeping their filenames.

## Sources and attribution

Every external source is cited as an outbound hyperlink with attribution text. No third-party prose, figures, or datasets are reproduced anywhere in these files — all writing, examples, and diagrams here are original. Cited sources include Wikipedia, arXiv, Hugging Face documentation, the OpenAI API reference, the Google SRE Book, and Chris Olah's writing on information theory.

Because nothing is copied, no source license's share-alike or attribution obligations attach to this material.

## On how this was made

This course was generated with AI assistance and reviewed by a human. The subject matter is well-established, slow-moving statistics rather than a fast-changing field, and the material has been spot-checked for accuracy — but it is a strong first pass, not a peer-reviewed textbook. Treat it as a study aid. If a specific claim matters for something load-bearing, follow the citation and verify it at the source.

Corrections are welcome.

## License

[Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) — see [LICENSE](LICENSE).

You are free to share and adapt this material for any purpose, including commercially, as long as you give appropriate credit, link to the license, and indicate whether you made changes.

Suggested attribution:

> "Statistics for AI, GenAI & LLMs" by Stylus Nexus, licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Source: https://github.com/stylusnexus/statistics-for-ai

The license covers the original material in this repository — the lesson prose, examples, diagrams, flashcards, and course structure. Linked third-party sources remain under their own terms.
