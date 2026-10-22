# Statistics for AI, GenAI & LLMs

Two self-paced courses. **Statistics for AI, GenAI & LLMs** has nineteen lessons on the statistics behind building and judging AI systems. **Math for AI: Linear Algebra & Calculus Essentials** has seven lessons on the linear algebra and calculus underneath them.

Every example comes from AI and ML work: RAG chunk-size distributions, hallucination-detector base rates, latency percentiles, softmax temperature, cross-entropy loss. Most statistics material opens with dice and urns instead. The lessons are deliberately sequential: each one assumes only what came before it, and no prior statistics background is required. The math course needs only high-school algebra.

## Start here

Open **`index.html`** (statistics) or **`math.html`** (math) in any browser. That's the whole setup — no install, no build step, no dependencies.

Each lesson is a single self-contained HTML file with inline styles and inline SVG diagrams. There are no external scripts, stylesheets, fonts, or images. The lessons render the same from a local file, a static host, or a sandboxed iframe.

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

Seventy-six cards, four per lesson, in `Statistics for AI, GenAI & LLMs.md`. The math course has its own 28-card deck in `ai-math-foundations/review/`. They test active recall. Most pose a scenario and ask what breaks or what you would do. None ask you to match a term to a definition.

The format is `question :: answer`, one card per line, which [Obsidian_to_Anki](https://github.com/Pseudonium/Obsidian_to_Anki) imports directly. Without that plugin it still reads fine as plain Markdown.

`Statistics for AI, GenAI & LLMs.json` holds three escalating hints per card (nudge → structural hint → keyword). It's consumed by the EngramQuest review UI and isn't especially useful standalone.

### Memory map — `ai-statistics-foundations-memory.canvas`

A 99-node [JSON Canvas](https://jsoncanvas.org/) lays out the whole statistics course as one picture. It has one cluster per lesson, with edges showing how each concept feeds the next. The math course has its own map, `ai-math-foundations/ai-math-foundations-memory.canvas`. It renders in Obsidian. The file-node links are vault-relative. They resolve after you import the course folder (below), not when you browse this repo.

## Using this inside Obsidian

The `ai-statistics-foundations/` and `ai-math-foundations/` folders are each a drop-in EngramQuest Lesson Academy course. Copy the whole folder into your vault at:

```
<vault>/engram-quest/lessons/ai-statistics-foundations/
<vault>/engram-quest/lessons/ai-math-foundations/
```

Do not rename the folder. Its name is already the course slug the plugin expects. `meta.json` ships with all progress flags set to `false`, so the course registers as unstarted.

To add a review deck, copy the two files from that course's `review/` folder into your vault. Put the Markdown file in `engram-review/ai-cards/` and the JSON file in `engram-review/hints/`. Keep the filenames.

## Sources and attribution

Every external source is cited as an outbound hyperlink with attribution text. These files reproduce no third-party prose, figures, or datasets. All writing, examples, and diagrams are original. Cited sources include Wikipedia, arXiv, Hugging Face documentation, the OpenAI API reference, the Google SRE Book, and Chris Olah's writing on information theory.

Because nothing is copied, no source license's share-alike or attribution obligations attach to this material.

## On how this was made

This course was generated with AI assistance and reviewed by a human. The subject is well-established statistics, not a fast-changing field, and the material has been spot-checked for accuracy. It is a strong first pass, not a peer-reviewed textbook. Treat it as a study aid. If a specific claim matters for something load-bearing, follow the citation and verify it at the source.

Statistics lessons 11–19 and the whole math course were written with AI assistance. Separate AI reviewers recomputed every worked number in Python and checked each quiz answer. No human has reviewed them line by line yet. A human reviewed lessons 1–10. One correction has been made since, in Lesson 9. An N = 500 example claimed a small p-value, but the exact figure is p ≈ 0.058.

Corrections are welcome.

## License

[Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) — see [LICENSE](LICENSE).

You may share and adapt this material for any purpose, including commercially. You must give credit, link to the license, and say whether you made changes.

Suggested attribution:

> "Statistics for AI, GenAI & LLMs" by Stylus Nexus, licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Source: https://github.com/stylusnexus/statistics-for-ai

The license covers the original material in this repository — the lesson prose, examples, diagrams, flashcards, and course structure. Linked third-party sources remain under their own terms.
