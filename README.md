# Statistics for AI, GenAI & LLMs

Two self-paced courses: **Statistics for AI, GenAI & LLMs** (nineteen lessons) covering the statistics you actually need to build, evaluate, and reason about AI systems, generative models, and LLMs, and a companion, **Math for AI: Linear Algebra & Calculus Essentials** (seven lessons), covering the vectors, matrices, derivatives, and integrals underneath them.

Every example is drawn from AI and ML work — RAG chunk-size distributions, hallucination-detector base rates, latency percentiles, softmax temperature, cross-entropy loss — rather than the dice and urns most statistics material opens with. The lessons are deliberately sequential: each one assumes only what came before it, and no prior statistics background is required. The math course needs only high-school algebra.

## Start here

Open **`index.html`** (statistics) or **`math.html`** (math) in any browser. That's the whole setup — no install, no build step, no dependencies.

Each lesson is a single self-contained HTML file with inline styles and inline SVG diagrams. There are no external scripts, stylesheets, fonts, or images, so the lessons render identically from a local file, a static host, or inside a sandboxed iframe.

## The statistics lessons

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
| 11 | Counting & Combinatorics | Multiplication rule, permutations and combinations, the binomial distribution, hash collisions |
| 12 | Random Variables | Random variables, expected value, variance, rules for combining them |
| 13 | Continuous Distributions | Density and the CDF; uniform, exponential, gamma and beta distributions |
| 14 | Joint Distributions | Joint, marginal and conditional distributions; independence; covariance; the chain rule |
| 15 | Limit Theorems & Hypothesis Testing | Law of large numbers, p-values, z- and t-tests, power, multiple comparisons |
| 16 | Bayesian Inference | Priors and posteriors, Beta-Binomial updating, MAP vs. MLE, credible intervals |
| 17 | Study Design & Confounding | Random sampling, confounding, Simpson's paradox, randomized tests, data leakage |
| 18 | Resampling: Bootstrap & Cross-Validation | Bootstrap, paired bootstrap, k-fold cross-validation |
| 19 | Multiple & Logistic Regression, ANOVA | Multiple regression, logistic regression, one-way ANOVA |

Lessons 1–10 run from describing data to the mechanics inside an LLM. Lessons 11–19 add the probability, inference, and modelling fundamentals underneath them, for readers who want the full foundation.

## The math lessons

| # | Lesson | Covers |
|---|--------|--------|
| 1 | Vectors | Dot products, norms, cosine similarity, embeddings |
| 2 | Matrices | Matrix multiplication, layers, shapes, attention scores |
| 3 | Eigenvectors, SVD & Low-Rank Structure | PCA, low-rank approximation, LoRA |
| 4 | Derivatives | Slope, rules, sigmoid, ReLU, log loss, gradient checking |
| 5 | Partial Derivatives & Gradients | Gradient descent, learning rate, loss surfaces |
| 6 | The Chain Rule & Backpropagation | Computational graphs, a full hand-worked backprop, vanishing gradients |
| 7 | Integrals | Area, the fundamental theorem, densities, expectation, Monte Carlo |

Each lesson ends with a retry-friendly quiz.

## What else is in here

### Flashcards — `ai-statistics-foundations/review/`

Seventy-six cards, four per lesson, in `Statistics for AI, GenAI & LLMs.md`. The math course has its own 28-card deck in `ai-math-foundations/review/`. They're written for active recall — most pose a scenario and ask what breaks or what you'd do — rather than asking you to match a term to its definition.

The format is `question :: answer`, one card per line, which [Obsidian_to_Anki](https://github.com/Pseudonium/Obsidian_to_Anki) imports directly. Without that plugin it still reads fine as plain Markdown.

`Statistics for AI, GenAI & LLMs.json` holds three escalating hints per card (nudge → structural hint → keyword). It's consumed by the EngramQuest review UI and isn't especially useful standalone.

### Memory map — `ai-statistics-foundations-memory.canvas`

A 99-node [JSON Canvas](https://jsoncanvas.org/) laying out the whole statistics course as one picture (the math course has its own, `ai-math-foundations/ai-math-foundations-memory.canvas`): one cluster per lesson, with edges showing how each concept feeds the next. It renders in Obsidian. The file-node links inside it are vault-relative, so they resolve once the course folder is imported (below) — not when browsing this repo directly.

## Using this inside Obsidian

The `ai-statistics-foundations/` and `ai-math-foundations/` folders are each a drop-in EngramQuest Lesson Academy course. Copy the whole folder into your vault at:

```
<vault>/engram-quest/lessons/ai-statistics-foundations/
<vault>/engram-quest/lessons/ai-math-foundations/
```

No renaming needed — the folder name is already the course slug the plugin expects. `meta.json` ships with all progress flags set to `false`, so it registers as a fresh, unstarted course.

To also wire up a review deck, copy the two files from that course's `review/` folder into your vault's `engram-review/ai-cards/` and `engram-review/hints/` respectively, keeping their filenames.

## Sources and attribution

Every external source is cited as an outbound hyperlink with attribution text. No third-party prose, figures, or datasets are reproduced anywhere in these files — all writing, examples, and diagrams here are original. Cited sources include Wikipedia, arXiv, Hugging Face documentation, the OpenAI API reference, the Google SRE Book, and Chris Olah's writing on information theory.

Because nothing is copied, no source license's share-alike or attribution obligations attach to this material.

## On how this was made

This course was generated with AI assistance and reviewed by a human. The subject matter is well-established, slow-moving statistics rather than a fast-changing field, and the material has been spot-checked for accuracy — but it is a strong first pass, not a peer-reviewed textbook. Treat it as a study aid. If a specific claim matters for something load-bearing, follow the citation and verify it at the source.

Statistics lessons 11–19 and the whole math course were written with AI assistance and checked by separate AI reviewers who recomputed every worked number in Python and checked each quiz answer. No human has reviewed them line by line yet. Lessons 1–10 were reviewed by a human; the one correction made to them since is in Lesson 9, where an N = 500 example claimed a small p-value when the exact figure is p ≈ 0.058.

Corrections are welcome.

## License

[Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) — see [LICENSE](LICENSE).

You are free to share and adapt this material for any purpose, including commercially, as long as you give appropriate credit, link to the license, and indicate whether you made changes.

Suggested attribution:

> "Statistics for AI, GenAI & LLMs" by Stylus Nexus, licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Source: https://github.com/stylusnexus/statistics-for-ai

The license covers the original material in this repository — the lesson prose, examples, diagrams, flashcards, and course structure. Linked third-party sources remain under their own terms.
