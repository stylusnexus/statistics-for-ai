---
tags:
  - flashcards/ai-statistics-foundations
TARGET DECK: ai-statistics-foundations
---

## Lesson 1 — Why Statistics Powers AI

In a RAG pipeline, why can locking in a 500-token chunk size before looking at your document set backfire — and which statistical step would catch it first? :: Without checking the mean chunk length, its spread, and whether a handful of very long documents are dragging the average up, a "reasonable-feeling" fixed chunk size quietly mangles your longest and shortest documents. Describing the data first (mean, variance, shape) is the look-before-you-leap step that catches this before it ships.

Why does a spam filter or content-moderation API output a probability like 0.92 instead of a flat spam/not-spam label? :: A probability lets you (or a downstream system) choose the decision threshold to match the stakes — a strict threshold for a wire-transfer fraud alert, a looser one for a newsletter. A binary label throws that control away.

Your new agent prompt scores higher on 5 test cases than the old one. Per this course, what do you actually need before calling it "better"? :: Evidence the improvement is statistically real and not just sampling noise from a tiny test set — 5 examples isn't enough to distinguish a genuine gain from luck.

If an LLM answers the same question correctly 9 times out of 10, what's the risk in treating that as "it works"? :: The model's output is a sample from a probability distribution, not a fact — a 9/10 hit rate still means it will be confidently, fluently wrong the 10th time. Without statistics describing that variability, "it worked when I tried it" tells you almost nothing about production reliability.

## Lesson 2 — Describing Data: Mean, Variance & Distribution Shape

A dataset of 10 LLM response lengths is mostly ~120 tokens, but one runaway generation hits 980 tokens. Why does the mean report ~208 tokens while the median stays near 121 — and which one describes a "typical" response? :: The mean sums every value and divides, so the single 980-token outlier drags it up by nearly 90 tokens. The median only looks at the middle value(s) and barely moves. The median (121.5) is what a typical response actually looks like; the mean (207.8) doesn't match any single response in the dataset.

When computing spread in a dataset, why can't you just average the raw deviations from the mean instead of squaring them first? :: Raw deviations from the mean always sum to exactly zero — it's a mathematical guarantee, since the mean is the balancing point of the data. Squaring removes the sign so the spread doesn't cancel itself out, and it's also smooth/differentiable (needed for gradient-based training) and punishes large errors disproportionately.

A team reports "average API latency: 220ms" and ships. Why should that number alone worry you? :: Latency is right-skewed — most requests are fast, a rare few are very slow (cold starts, retries, GPU queues), and the mean gets pulled up by that long tail. The "average" hides the worst-case tail; production teams instead report the median (typical experience) alongside a high percentile like p95 or p99 to expose it.

A spam classifier's training set is 95% "not spam" and 5% "spam." Why is this the same underlying problem as a right-skewed latency distribution? :: Both are distribution-shape problems: skew lets the mean misrepresent the "typical" value, and class imbalance lets a model hit 95% accuracy by always predicting "not spam" — never actually learning what spam looks like. Naive metrics (mean, accuracy) get fooled by shape in both cases.

## Lesson 3 — Probability Foundations & Bayes' Theorem

A hallucination detector catches 90% of real hallucinations (true-positive rate) and false-flags only 5% of clean responses, on a 2% true hallucination base rate. Why is it still wrong about 73% of the time when it raises a flag? :: Because hallucinations are rare (2% of 10,000 = 200) versus clean responses (9,800), even a small 5% false-positive rate on the huge clean population produces 490 false alarms versus only 180 true positives. Posterior P(hallucination | flagged) = 180/670 ≈ 27% correct — so ~73% of flags are false alarms. A low base rate can dominate a good detector's precision.

What's the difference between "this model is 90% accurate" and "when this model flags something, it's right 90% of the time" — and why does confusing them matter? :: The first is a likelihood claim: P(evidence | true condition) — how the detector behaves given the true label. The second is a posterior claim: P(true condition | evidence) — what the true label is given the detector's output. They can differ enormously because the posterior also depends on the base rate.

In Bayes' theorem terms, what makes a spam filter's classification "literally computing an approximation of P(class | features)" rather than just pattern-matching keywords? :: It combines a prior (baseline probability of spam before seeing the message), a likelihood (how probable these specific words are given spam vs. not-spam), and normalizes by the evidence (how probable these words are overall) — the prior → likelihood → posterior structure of Bayes' theorem, which is exactly what "Naive Bayes" classifiers compute.

Two events are independent if P(A given B) equals P(A). Why does an LLM API call's day-of-week and whether that response contains a hallucination illustrate independence, while an email's wording and whether it's spam do not? :: Knowing the day of the week tells you essentially nothing about hallucination risk, so they're independent. But specific words like "free" shift the probability of spam sharply upward, so wording and spam status are dependent — and that dependence is exactly what classifiers exploit.

## Lesson 4 — The Distributions That Run Machine Learning

When an LLM generates its next token, its final softmax layer outputs one probability per vocabulary entry (often 30,000-100,000+), all positive and summing to exactly 1. Which named distribution is this, and why does that framing matter? :: It's a categorical distribution — K = vocabulary size, with the K probabilities recomputed fresh at every token position. This framing is what makes "generating text" precisely defined: sampling one outcome from a freshly-computed categorical distribution, over and over, one token at a time.

A classifier outputs 0.83 as its predicted probability that an email is spam. Is that predicted score itself Bernoulli-distributed? Why or why not? :: No — 0.83 is a single number, not a distribution. What's Bernoulli-distributed is the underlying true label (spam=1 / not-spam=0), with parameter p equal to the true base rate of spam in traffic. Don't confuse the model's output score with the distribution of the true label.

Your API traffic averages λ=2 requests per second. Using a Poisson model, why is "exactly 2 requests next second" not actually the most likely single outcome by a wide margin, and what does knowing λ let you do for capacity planning? :: P(2) ≈ 27.1%, tied with P(1) ≈ 27.1% as the most likely counts — the distribution spreads probability across nearby values instead of concentrating on the average. Naming the Poisson distribution and its rate λ lets you estimate how often you'll see spikes (e.g., 6+ requests) for autoscaling, without needing raw historical data for every possible count.

Why are diffusion image models (Stable Diffusion, DALL-E, Midjourney) built around the Normal distribution specifically, rather than some other distribution? :: Training repeatedly adds Gaussian (Normal) noise to an image until it becomes pure static, then the model learns to reverse that process one step at a time. The Normal distribution's symmetric, parameter-controlled spread (mean, variance) is what makes each noising/denoising step mathematically well-behaved and reversible.

## Lesson 5 — From Sample to Population: Estimation & the CLT

Your team says "we ran the new prompt on our 20-example eval set and it scored 5 points higher." Why does this course call that claim "close to meaningless"? :: With only 20 examples, a 5-point swing is well within the range you'd expect from random sampling noise even if the two prompts were truly identical — small samples lie by accident. At N=20, the plausible range around a true 80% accuracy can span roughly 65%-93%, so a small headline gap could vanish or reverse with different examples.

Standard error shrinks as standard deviation divided by the square root of N. Why does quadrupling your eval set from 100 to 400 examples only cut your uncertainty in half, not to a quarter? :: Standard error scales with the square root of N, not N itself — going from N=100 (SE=0.040) to N=400 (SE=0.020) requires 4x the data to halve the uncertainty. This "root-N tax" is why doubling an eval set's size gives diminishing returns on precision.

In what sense is training a model — minimizing cross-entropy loss — mathematically the same procedure as Maximum Likelihood Estimation (MLE)? :: Cross-entropy loss is, mathematically, negative log-likelihood — MLE in disguise. Both ask the same question: which parameter values make the data you actually observed most probable? A coin-flip proportion and a training run minimizing cross-entropy are the same underlying computation at different scales.

Model A scores 0.84 and Model B scores 0.81 on 100-example eval sets. Why doesn't this prove Model A is better? :: Each score carries a standard error, so the two models' plausible ranges overlap heavily. The 3-point gap could easily be sampling noise rather than a real capability gap. Reporting a bare average without its standard error is one of the most common ways AI teams fool themselves about progress.

## Lesson 6 — Correlation, Regression & Why Models "Fit" Data

You observe that conversations with more emoji correlate with lower toxicity scores, so you consider suppressing emoji-heavy messages as an anti-toxicity rule. What question should you ask before shipping that rule? :: Is there a third factor that could explain both sides? A friendly, casual conversation style might independently drive both more emoji use and lower toxicity — emoji was never the cause. Shipping the rule would degrade the product for friendly users while barely touching actual toxic content. Correlation is not causation.

Model size vs. benchmark score across 8 checkpoints gives r ≈ 0.99, while prompt length vs. response quality gives r ≈ 0.15. What does the correlation coefficient r actually measure, and what can an r near 0 miss? :: r measures the strength and direction of a linear (straight-line) relationship, ranging -1 to +1. An r near 0 only rules out a straight-line pattern — it can still miss a real nonlinear relationship (a curve, U-shape, or threshold effect). Always look at the scatter plot, not just the number.

Fitting a line to model-size-vs-benchmark-score data and training a modern LLM both follow the exact same three-step loop. What is that loop? :: Define parameters (slope/intercept for a line; billions of weights for an LLM), define a loss that measures how wrong the current parameters are (sum of squared residuals for regression; cross-entropy for an LLM), and search for the parameter values that minimize that loss. Linear regression IS training a model — the smallest, simplest one that exists.

A regression line has R² ≈ 0.99 on your training checkpoints. What does that number tell you, and what does it NOT guarantee? :: R² ≈ 0.99 means about 99% of the variation in the outcome is explained by the line on this data — it's a good summary of this data. It does NOT mean the relationship is causal, and it does NOT guarantee the line will still fit well on data very different from what it was fit on.

## Lesson 7 — Information Theory for AI: Entropy, Cross-Entropy & KL Divergence

A next-token distribution assigns 92% probability to "Paris" for "The capital of France is ___", versus a near-uniform spread for "My favorite color is ___". Which one has higher entropy, and what does entropy actually measure here? :: "My favorite color is ___" has higher entropy because entropy measures uncertainty/spread, not correctness. High entropy means probability is spread thin across many outcomes (low confidence); low entropy means it's concentrated on one outcome (high confidence). Entropy tells you how certain the model is, not how correct it is.

A model puts 70% probability on the correct next token ("Paris") vs. a different model that puts only 5% on "Paris" and 65% on the wrong answer ("London"). Both cases use cross-entropy loss — why does the second cost roughly 8x more? :: Cross-entropy loss for a one-hot true label is -log₂(probability assigned to the correct answer). At 70% correct, loss ≈ 0.51 bits; at 5% correct, loss ≈ 4.32 bits — about 8.5x higher. The logarithm makes being confidently wrong disproportionately expensive compared to being moderately wrong, which is exactly the signal you want during training.

Entropy, cross-entropy, and KL divergence are related by cross-entropy = entropy + KL divergence. For LLM next-token training, why do cross-entropy and KL divergence end up numerically identical? :: True labels for next-token prediction are one-hot (100% probability on the actual next token), so the true distribution's entropy is zero. That collapses the equation to cross-entropy = KL divergence — they become the same number in this specific case.

During RLHF fine-tuning (e.g. training a model toward human-preferred responses), what does the KL-divergence penalty against a frozen reference model prevent, and why is it needed? :: Without it, the model can "reward hack" — drifting into strange, repetitive, degenerate text that scores well on the reward signal but is no longer coherent. The KL penalty acts as a leash, keeping the fine-tuned output distribution from straying too far from the original pretrained distribution while it optimizes for human preference.

## Lesson 8 — The Statistics Behind Training a Model

During training, loss drops from 2.41 to 0.09 over 1000 steps on the training set — but validation loss stalls or rises partway through. What's happening, and why is "training loss going down" not by itself good news? :: This is overfitting — the model is memorizing the training data (including its noise) rather than learning generalizable patterns. Training loss only measures fit to data the model has already seen; validation loss (measured on held-out data) is what tells you whether that improvement is transferring to anything new.

You fine-tune a general-purpose LLM on a small, narrow support-ticket dataset. It answers the exact training questions perfectly, but accuracy falls off a cliff when the same question is reworded. What's this failure mode called, and what's the risk of over-training on a narrow fine-tune? :: This is overfitting (catastrophic forgetting in the extreme case) — the model memorized surface patterns from the narrow dataset rather than the underlying task, and can even lose general abilities it had before fine-tuning. A model that scores near-perfectly on its own training set may just be an expensive lookup table.

As model complexity increases, bias falls but variance rises, producing a U-shaped total-error curve. What does "training a model well" actually mean under the bias-variance tradeoff, and why can't you eliminate both errors at once? :: There's no model with zero bias and zero variance simultaneously — training well means landing near the bottom of the U-shaped curve where the two error sources balance, not eliminating error altogether. A too-simple model underfits (high bias); a too-complex or undertrained model overfits (high variance).

A tiny model asked to handle a genuinely complex task shows flat, stubbornly high loss on both its training data and validation data. Is this overfitting or underfitting, and how can you tell? :: This is underfitting — the signature is training AND validation loss both staying high and close together (versus overfitting, where training loss drops while validation loss stalls or rises). The model is too simple or undertrained to capture the pattern at all.

## Lesson 9 — Evaluating AI Models Rigorously

A content-moderation model predicts "clean" for every one of 1,000 posts, of which 50 actually violate policy. It scores 95% accuracy. What do precision and recall reveal that accuracy hides? :: Precision is undefined/0% (it never flagged anything) and recall is 0% (it caught none of the 50 real violations). The model is a complete failure at its actual job despite a headline 95% accuracy — accuracy is gamed by the 95%-clean class imbalance, while precision and recall can't be faked this way.

A hallucination detector has precision 0.9 but recall 0.1. Why is its F1 score 0.18 rather than the simple average of 0.5? :: F1 is the harmonic mean of precision and recall, which punishes large gaps between them much more severely than an arithmetic average would. F1 = 2×(0.9×0.1)/(0.9+0.1) = 0.18 — reflecting that a detector catching only 10% of real cases is bad even if the few it catches are almost always correct.

Lowering a hallucination detector's flagging threshold increases recall but decreases precision. Why can't you have both, and how would you decide which threshold to use for a medical-advice chatbot versus a casual chat app? :: A lower threshold flags more cases, catching more real hallucinations (higher recall) but also triggering more false alarms (lower precision) — there's no universally correct threshold. A medical-advice chatbot should use a low threshold (catching hallucinations matters more than false alarms); a casual chat app can tolerate a higher threshold (fewer interruptions matters more).

Prompt A scores 75% and Prompt B scores 80% on a 20-example eval set (a 5-point gap). The exact same percentage gap appears when both are evaluated on 500 examples. Why does the sample size change whether you should trust the gap as real? :: On N=20, the 5-point gap is exactly one flipped example — well within what random sampling noise would produce, not statistically significant. On N=500, the same percentage gap represents 25 more passing examples out of many more trials — a real, reproducible difference. Same headline number, opposite conclusion, purely because of sample size.

## Lesson 10 — The Statistics Unique to LLMs

Model A has perplexity 12.3 and Model B has perplexity 18.7 on the same eval set. What does that comparison actually mean in terms of the models' next-token choices, and how is perplexity computed from cross-entropy? :: Perplexity = exp(cross-entropy loss) — it converts the loss into an interpretable scale: roughly "how many plausible options the model was choosing among at each step." Model A was effectively choosing among ~12 plausible next tokens per position, Model B among ~19 — lower perplexity means the model narrows down the vocabulary more sharply.

Lowering temperature sharpens a next-token distribution (lower entropy, more deterministic) and top-k/top-p truncation also lowers entropy by cutting off the tail. Why doesn't setting a very low temperature make top-k or top-p truncation redundant? :: Temperature continuously reshapes every probability in the distribution but never fully deletes any option — a low-probability token can still theoretically be drawn. Top-k and top-p instead draw a hard line and delete the tail entirely, regardless of how temperature reshaped things elsewhere. They're often combined in production because they control different things.

A model's predictions in the 90-100% confidence bucket are only actually correct 72% of the time — a 23-point overconfidence gap. Why is this a calibration problem distinct from "the model is often wrong," and why does it matter for auto-approval systems? :: Calibration measures whether stated confidence matches actual accuracy, which is separate from raw accuracy itself — a model can be reasonably accurate overall while still being poorly calibrated. A system that auto-approves anything above 90% confidence will inherit that systematic overconfidence, because the confidence number itself isn't a safe decision gate here.

"The cat flew across the room" is fluent and grammatical, but "flew" had only a 4% base probability at that position. What does this reveal about what hallucination statistically is? :: Hallucination isn't a categorical malfunction — it's what happens when you sample from a probability distribution and either draw a legitimately low-probability-but-real continuation, or the distribution itself was miscalibrated in a sparse data region and the model confidently extrapolated wrong. Both causes are influenced by temperature, truncation, and calibration.
