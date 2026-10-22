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

Prompt A scores 75% and Prompt B scores 80% on a 20-example eval set (a 5-point gap). The exact same percentage gap appears when both are evaluated on 500 examples. Why does the sample size change whether you should trust the gap as real? :: On N=20, the 5-point gap is exactly one flipped example — well within what random sampling noise would produce, not statistically significant. On N=500, the same percentage gap represents 25 more passing examples out of many more trials — z ≈ 1.89, p ≈ 0.058, much stronger evidence though still just short of the usual 0.05 bar. Same headline number, very different strength of evidence, purely because of sample size.

## Lesson 10 — The Statistics Unique to LLMs

Model A has perplexity 12.3 and Model B has perplexity 18.7 on the same eval set. What does that comparison actually mean in terms of the models' next-token choices, and how is perplexity computed from cross-entropy? :: Perplexity = exp(cross-entropy loss) — it converts the loss into an interpretable scale: roughly "how many plausible options the model was choosing among at each step." Model A was effectively choosing among ~12 plausible next tokens per position, Model B among ~19 — lower perplexity means the model narrows down the vocabulary more sharply.

Lowering temperature sharpens a next-token distribution (lower entropy, more deterministic) and top-k/top-p truncation also lowers entropy by cutting off the tail. Why doesn't setting a very low temperature make top-k or top-p truncation redundant? :: Temperature continuously reshapes every probability in the distribution but never fully deletes any option — a low-probability token can still theoretically be drawn. Top-k and top-p instead draw a hard line and delete the tail entirely, regardless of how temperature reshaped things elsewhere. They're often combined in production because they control different things.

A model's predictions in the 90-100% confidence bucket are only actually correct 72% of the time — a 23-point overconfidence gap. Why is this a calibration problem distinct from "the model is often wrong," and why does it matter for auto-approval systems? :: Calibration measures whether stated confidence matches actual accuracy, which is separate from raw accuracy itself — a model can be reasonably accurate overall while still being poorly calibrated. A system that auto-approves anything above 90% confidence will inherit that systematic overconfidence, because the confidence number itself isn't a safe decision gate here.

"The cat flew across the room" is fluent and grammatical, but "flew" had only a 4% base probability at that position. What does this reveal about what hallucination statistically is? :: Hallucination isn't a categorical malfunction — it's what happens when you sample from a probability distribution and either draw a legitimately low-probability-but-real continuation, or the distribution itself was miscalibrated in a sparse data region and the model confidently extrapolated wrong. Both causes are influenced by temperature, truncation, and calibration.

## Lesson 21 — Counting & Combinatorics: How Big Is the Space of Possibilities?

A teammate proposes finding the best 20-token reply by scoring every possible sequence from a 50,000-token vocabulary. Why does this fail, and how do decoders get around it? :: By the multiplication rule there are 50,000^20 ≈ 9.5 × 10^93 sequences, far more than can ever be scored. Decoders build the answer one token at a time (greedy or sampling) or keep a small shortlist per step (top-k, beam search) instead of searching the whole space.

A RAG system retrieves 20 chunks and puts 5 into the prompt. How many different sets of 5 exist, and how many distinct prompts if the order of chunks also matters? :: Sets: C(20,5) = 20!/(5!·15!) = 15,504. Ordered prompts: 20×19×18×17×16 = 1,860,480, which is 15,504 × 5! (each set can be arranged 120 ways). Order-free counts divide by k!.

A model's true per-prompt accuracy is 0.8 and you test it on 10 independent prompts. What is P(exactly 8 correct), and what does that tell you about trusting a 10-prompt eval? :: P(8) = C(10,8) × 0.8^8 × 0.2^2 = 45 × 0.16777 × 0.04 ≈ 0.302; mean np = 8, variance np(1−p) = 1.6, sd ≈ 1.26. Even the single most likely score happens only about 30% of the time, and 6/10 or worse happens about 12% of the time, so a small eval is noisy.

You dedupe 100,000 documents by 32-bit hash and assume equal hashes mean equal documents. Why is that unsafe, and roughly how likely is at least one false match? :: Pairs grow like n²: 100,000 × 99,999 / 2 ≈ 5 × 10^9 pairs, each colliding with probability about 1/2^32, so about 1.16 expected collisions and P(at least one) ≈ 1 − e^−1.16 ≈ 69%. This is the birthday effect: collisions arrive near √N draws, not N. Use a longer hash or confirm matches.

## Lesson 22 — Random Variables: Expectation, Variance & the Rules for Combining Them

Your response-length distribution is 100 tokens (0.4), 200 (0.3), 400 (0.2), 800 (0.1) and output costs 2 micro-dollars per token plus a fixed 400 for the prompt. A teammate budgets using the most common length. What do you use instead, and what do you get? :: Use the expected value, the probability-weighted average: E[X] = 40 + 60 + 80 + 80 = 260 tokens. By linearity, E[2X + 400] = 2(260) + 400 = 920 micro-dollars per request. The most common length (100 tokens, cost 600) understates the budget because the rare 800-token responses pull the mean up. Also check the tail: 10% of requests cost 2,000.

A three-step agent has independent step latencies with SDs of 30 ms, 200 ms, and 40 ms. A colleague says the pipeline SD is 270 ms. What is wrong, and what is the right number? :: Standard deviations do not add; variances do, when the steps are independent. Variances are 900 + 40,000 + 1,600 = 42,500 ms squared, so the SD is about 206 ms. The LLM step supplies 40,000 of the 42,500, so it is the step to work on. If the steps share a cause such as a loaded GPU, they are not independent and the real spread can be larger.

You change the cost formula from Y = 2X + 400 to Y = 2X + 4,000 (a bigger fixed prompt charge), with Var(X) = 44,400. What happens to the mean cost and to the variance of the cost? :: The mean rises by 3,600 because E[aX + b] = aE[X] + b. The variance is unchanged at 4 x 44,400 = 177,600 because Var(aX + b) = a squared times Var(X): a constant shift moves every value equally and does not change the spread. Only scaling the variable (the 2) affects variance.

Single eval questions score 1 or 0 with p = 0.8, so one question has SD 0.4. You average over 100 independent questions and then decide to quadruple the eval set to 400. How much does the noise in the measured accuracy drop at each size, and what is the lesson? :: SD of an average is sigma divided by the square root of n: 0.4 / 10 = 0.04 at n = 100, and 0.4 / 20 = 0.02 at n = 400. Quadrupling the data only halves the noise, so small accuracy differences cost a lot of extra data to resolve. The mean accuracy stays 0.8 either way.

## Lesson 23 — Continuous Distributions: Density, the CDF, and the Shapes Beyond the Bell Curve

A teammate reads a density chart of request gaps, sees the curve start at height 2.0, and says there is a 200% chance of a very short gap. What is wrong, and what is the real probability of a gap under 0.5 s when the density is 2e^(-2t)? :: Height is a density, not a probability; densities can exceed 1 and a single exact value has probability 0. Probability is area under the curve: P(0 <= T <= 0.5) = 1 - e^(-1) = 0.632, and the whole curve encloses area 1.

Gaps between requests have CDF F(t) = 1 - e^(-2t). Your SLO dashboard needs the p95 gap and you notice the median is below the mean. How do you get both, and what does the difference tell you? :: Read the CDF backwards. p95: 1 - e^(-2t) = 0.95 gives t = ln(20)/2 = 1.498 s. Median: t = ln(2)/2 = 0.347 s, below the mean of 0.5 s. A median under the mean means a right-skewed tail pulling the average up, so report percentiles, not the mean.

Requests arrive at 2 per second with exponential gaps. A batcher waits for the 3rd request, and 0.5 s have already passed with none. What are the mean and variance of a fresh 3-request wait, and does the silence so far change the wait for the next request? :: The wait for the 3rd request is gamma: mean k/lambda = 3/2 = 1.5 s, variance k/lambda^2 = 3/4 = 0.75 (sd 0.866 s). The silence changes nothing for the next arrival: the exponential is memoryless, so P(wait > 1 s more | already waited 0.5 s) = e^(-2) = 0.135, same as a fresh wait.

Two eval reports both give a model's accuracy as a Beta distribution with mean 0.75: Beta(9, 3) from one run and Beta(30, 10) from a bigger one. How do they differ, and which would you trust for a ship decision? :: Both have mean alpha/(alpha+beta) = 0.75, but the spread differs: sd 0.120 for Beta(9, 3) versus 0.068 for Beta(30, 10), because alpha+beta is 12 versus 40. The chance the true accuracy lies in 0.70 to 0.80 is about 0.305 versus 0.535. Trust the tighter Beta(30, 10); the same average rests on more evidence.

## Lesson 24 — Joint Distributions: Several Variables at Once, Independence & Covariance

Your QA assistant reports 72% correct overall, with math queries making up 30% of traffic. Why is that pair of numbers not enough to know where it fails, and what do you build instead? :: Marginals can't be turned back into the joint, so they hide where the errors sit. Build the joint table of query type by correctness, then divide cells by column totals: P(correct | math) = 0.12 / 0.30 = 0.40 versus 0.36 / 0.40 = 0.90 for lookups.

In a log, P(Math) = 0.30, P(Correct) = 0.72 and P(Math, Correct) = 0.12. Are query type and correctness independent, and how do you know? :: No. Independence needs every joint cell to equal the product of its marginals: 0.30 x 0.72 = 0.216, but the observed cell is 0.12. One failing cell is enough, and it shows math answers are right far less often than chance pairing would predict.

Retrieval and generation times have Cov = +0.08 s^2, Var(retrieval) = 0.025 and Var(generation) = 0.30. A teammate says total latency variance is 0.325. What is wrong and what is the right figure? :: They dropped the covariance term. Var(X+Y) = Var(X) + Var(Y) + 2Cov(X,Y) = 0.025 + 0.30 + 0.16 = 0.485, so the SD is about 0.70 s, not 0.57 s. Slow stages coincide, so the bad cases stack and the tail is wider than the independent assumption says.

An LLM scores a three-token reply with P(x1) = 0.20, P(x2 | x1) = 0.05, P(x3 | x1, x2) = 0.30. What is the sequence probability, why can't a model store the joint table instead, and why do implementations add logs? :: Chain rule: 0.20 x 0.05 x 0.30 = 0.003, an identity needing no independence. A table over 50,000 tokens at three positions has 50,000^3 = 1.25 x 10^14 cells, so the model learns next-token conditionals instead. Logs avoid underflow: -1.609 - 2.996 - 1.204 = -5.809 = ln 0.003.

## Lesson 25 — Limit Theorems & Hypothesis Testing: When Is a Difference Real?

Prompt A passes 600/800 eval examples and Prompt B passes 640/800. How do you test whether B is really better, and what do you get? :: Two-proportion z-test. Pool both groups under the null: (600+640)/1600 = 0.775. SE = sqrt(0.775 x 0.225 x (1/800+1/800)) = 0.0209. z = 0.05/0.0209 = 2.39, two-sided p = 0.017, so reject at 0.05. The 95% interval for the gap is about +0.9 to +9.1 points: real, but possibly small.

Your teammate says p = 0.03 means there is a 3% chance the two prompts are equal. What is wrong, and what does it actually mean? :: A p-value is the chance of data at least this extreme if the null were true (P(data | null)), not the chance the null is true (P(null | data)). p = 0.03 means: if the prompts were truly equal, a gap this big would show up in about 3% of eval runs. It also says nothing about how large the gap is. Getting P(null | data) needs Bayes' Theorem.

You test 20 prompt variants against a baseline at alpha = 0.05 and one 'wins' with p = 0.02. Why shouldn't you ship it, and what threshold would you use instead? :: Even if none is truly better, 20 tests at 5% give 20 x 0.05 = 1 expected false positive and a 1 - 0.95^20 = 64% chance of at least one. Bonferroni tests each at 0.05/20 = 0.0025 (|z| > 3.02), so p = 0.02 fails. Confirm any winner on fresh held-out data.

A 0.2-point accuracy gain is 'highly significant' (p = 0.0004) on a 1,000,000-example test set. Should you ship the change automatically? :: Not automatically. The standard error shrinks with N (here about 0.00056), so a tiny gap becomes many standard errors from zero (z = 3.54). Significance says the gap is probably not zero; it does not say it is large. The 95% interval is only 0.09 to 0.31 points, so judge by effect size and cost, not the p-value.

## Lesson 26 — Bayesian Inference: Updating Beliefs About a Model, Not Just a Message

A team puts a Beta(8,2) prior on a new model's accuracy, then scores 6 correct out of 10 prompts. What is the posterior, its mean, and why does the mean land at 0.70 instead of the observed 0.60? :: Posterior = Beta(8+6, 2+4) = Beta(14,6), mean 14/20 = 0.70, 95% credible interval about 0.488 to 0.874. The prior counts as 10 imagined prompts and the eval as 10 real ones, so the mean is the halfway point between prior mean 0.80 and observed 0.60.

The same Beta(8,2) prior meets an eval of 600 correct out of 1000. Why does the prior barely matter now, and what are the posterior and its mean? :: Posterior = Beta(608,402), mean 608/1010 = 0.602. The prior carries weight 10/1010, about 1%, against 99% for the data, and the 95% credible interval tightens to about 0.572 to 0.632. A large eval overwhelms any reasonable prior; a 10-prompt eval cannot.

Your weight-decay setting adds lambda times the squared weights to the loss. What Bayesian belief are you encoding, and how does a tighter belief change lambda? :: A zero-mean Gaussian prior on the weights, so regularized training is MAP estimation: minus log posterior = data loss + ||w||^2 / (2 tau^2), giving lambda = 1/(2 tau^2). A tighter prior (tau = 0.5, lambda = 2) regularizes harder than a loose one (tau = 1, lambda = 0.5). A flat prior removes the penalty and MAP equals MLE.

A report says the 95% credible interval for accuracy is 0.49 to 0.87, and another says the 95% confidence interval is 0.29 to 0.91 on the same 6-of-10 eval. What can you say with each, and why are they so different? :: Credible: given the prior and the data, there is a 95% probability the true accuracy is in 0.49 to 0.87. Confidence: if you repeated the eval many times, about 95% of such intervals would cover the truth, with no probability claim about this one. The credible interval is narrower (0.39 wide against 0.62) because a Beta(8,2) prior adds 10 imagined prompts; that only helps if the prior is reasonable.

## Lesson 27 — Study Design: Randomization, Confounding & Data Leakage

A team builds its ticket-model eval only from tickets that already have an approved reference reply, which are mostly easy. Production is 55% easy (model right 92%) and 45% hard (right 64%). What does the eval report, what is the truth, and why won't more tickets of the same kind help? :: The eval reports 92%, the truth is 0.55*0.92 + 0.45*0.64 = 0.506 + 0.288 = 79.4%, a 12.6-point overstatement. It is a convenience sample filtered by difficulty, so more of the same just gives a tighter estimate of the wrong quantity. Sample at random from live traffic instead.

The old model beats the new one on easy tickets (93.1% vs 86.7%) and on hard tickets (73.0% vs 68.8%), yet the new one wins overall (82.6% vs 78.0%), both on 350 tickets. What happened, and which comparison do you trust? :: Simpson's paradox. The new model got 270 of 350 easy tickets (77.1%) while the old got 87 of 350 (24.9%), so the overall rates are weighted averages with different weights. Difficulty is a confounder, so trust the within-segment comparison, or better, rerun with random assignment.

An A/B test planned as 50/50 on 10,000 users ends with 5,200 in A and 4,800 in B. The lift looks good. What do you check first, with what number, and what does it mean? :: Check for sample ratio mismatch: chi-square = (200^2)/5000 + (200^2)/5000 = 8 + 8 = 16 on 1 degree of freedom, p about 0.00006. A gap that large from chance is far too unlikely, so assignment or logging is broken (crashes, caching, bot filtering) and the arms are not comparable. Find the cause before reading any metric.

A test set of 1,000 items secretly contains 300 exact copies of training items. The model gets 98% of the copies and 74% of the genuinely new items. What accuracy gets reported, how much is inflation, and what would you do? :: Reported = 0.30*0.98 + 0.70*0.74 = 0.294 + 0.518 = 81.2%, against a true 74.0%, so 7.2 points of inflation. Deduplicate by exact and near match, split by date where order matters, and never tune prompts or checkpoints against the test set.

## Lesson 28 — Resampling: The Bootstrap & Cross-Validation

A team logs 9 request latencies and reports a median of 232 ms. How do you get an interval for that median, and what do 10,000 bootstrap resamples (seed 42) give? :: Draw 9 latencies with replacement, take the median, repeat 10,000 times, then read the 2.5th and 97.5th percentiles of those medians: 205 ms to 410 ms. The interval is wide and lopsided because n is only 9 and two requests are slow (410 and 530), and only 9 distinct medians are even possible.

Two prompts are graded on the same 40 eval items: A passes 29, B passes 35, and they disagree on 8 items (7 for B, 1 for A). Why is a paired bootstrap of the +15 point gap better than resampling each prompt separately? :: Pairing keeps each item's two results together, so difficulty shared by both prompts cancels. The paired 95% interval is +2.5 to +27.5 points (excludes zero); the unpaired one is -2.5 to +32.5 (includes zero). Same data, different verdict, because only the 8 disagreeing items carry information.

Why does a bootstrap sample of n items contain only about 63.2% of the distinct originals, and what is the leftover set called? :: An item is missed by one draw with probability 1 - 1/n, so by all n draws with (1 - 1/n)^n, which tends to e^-1 = 0.368. So 1 - 0.368 = 0.632 of items appear (0.6513 at n=10, 0.6323 at n=1000; simulation gave 0.6323). The 36.8% left out are out-of-bag and can serve as a free validation set.

A team standardizes all features on the full dataset and then runs 5-fold cross-validation, getting 0.82. What went wrong, and what would you do differently? :: The scaler's statistics were computed using the validation examples, so each validation fold was not truly unseen: that is leakage, and the score is optimistic. Fit every data-dependent step (scaling, feature selection, vocabulary) on the training folds inside each round, and keep a separate test set sealed and score it once at the end.

## Lesson 29 — Beyond a Single Line: Multiple Regression, Logistic Regression & ANOVA

Prompt length alone has slope +4.28 on judge score, but in a model that also has passages retrieved its coefficient is -0.75 (95% CI -2.03 to +0.52). What happened, and what should you conclude? :: Longer prompts came with more passages (r = 0.78), so the one-feature slope was picking up the passages' effect. Holding passages fixed, length has no detectable effect: the interval spans zero. Passages (+7.41 per passage, CI 5.95 to 8.87) is what moves the score. A coefficient only means 'change per unit, holding the others fixed', and it changes with which features are in the model.

You add a second prompt-length feature (characters, r = 0.9985 with tokens). R-squared stays high but the token coefficient goes from -0.75 to +26.47 with SE 12.44. What is wrong and what do you do? :: Collinearity: the model cannot separate two near-identical features, so it splits credit arbitrarily. The SE grew 25 times (0.50 to 12.44), the VIFs are about 2,800 and 2,500, and the coefficients offset each other. Predictions are still fine, but attribution is not. Drop or combine one of the two features, then re-check the VIFs.

A logistic model for 'answer has an unsupported claim' has intercept -2.0, -0.9 per supporting passage and +0.7 per 100 answer tokens. What is p for 1 passage and 300 tokens, and what does adding one passage do? :: z = -2.0 - 0.9(1) + 0.7(3) = -0.8, so p = 1/(1 + e^0.8) = 0.31. One more passage multiplies the odds by e^-0.9 = 0.407: odds 0.449 become 0.183, so p falls to 0.15. That is an odds ratio, not a probability ratio, and the probability change would be smaller near 0 or 1.

Three prompt variants score means of 72, 79 and 74 (5 inputs each; SSB = 130, SSW = 66). What is F, what does p = 0.0015 let you say, and what can't you say? :: F = (130/2) / (66/12) = 65.0/5.5 = 11.82, p = (12/(12+2F))^6 = 0.0015. You can say at least one mean differs, tested once at 5% instead of running three t-tests (about 14% false-positive risk). You cannot say which: follow up with corrected pairs (Bonferroni 0.05/3 = 0.0167), which here show B beats A and C while A and C are indistinguishable.

