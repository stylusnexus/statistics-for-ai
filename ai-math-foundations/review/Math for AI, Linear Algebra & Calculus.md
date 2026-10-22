---
tags:
  - flashcards/ai-math-foundations
TARGET DECK: ai-math-foundations
---

## Lesson 1 — Vectors: Embeddings, Dot Products & Measuring Similarity

Your RAG index scores chunks by raw dot product on unnormalized embeddings, and the longest chunks keep coming back first even when shorter ones are more on-topic. What is going on, and what do you change? :: The dot product is ||q|| x ||c|| x cos(theta), so a chunk with a large vector length scores high even when its angle to the query is poor. Normalize all vectors to length 1 (or score with cosine similarity) so only the angle counts; for unit vectors the dot product equals the cosine.

Query q = (3, 1, 2); chunk A = (6, 2, 4) and chunk B = (2, 1, 2). Which chunk does cosine similarity prefer, and does Euclidean distance agree? :: Cosine prefers A: q.A = 28 and ||q|| x ||A|| = sqrt(14) x sqrt(56) = 28, so cosine = 1.000 (A is exactly 2q). B gives 11 / (3.742 x 3) = 0.980. Euclidean distance disagrees: ||q-A|| = sqrt(14) = 3.742 while ||q-B|| = 1.0, so distance prefers B. Cosine ignores length; distance does not.

You swap in a new embedding model and keep your old similarity cutoff of 0.80, and now almost everything passes. What assumption broke, and what do you do? :: Score distributions are model-specific: in hundreds or thousands of dimensions, unrelated texts cluster near some background cosine (about 0 for random directions with spread near 1/sqrt(d), higher for real models that use a narrow cone). Re-measure the cutoff on your own labeled queries for the new model rather than reusing 0.80.

Two vectors a = (2, -3, 0) and b = (3, 2, 5) are fed into a dot-product scorer. What score do you get and what does it say about the angle between them? :: a.b = 2x3 + (-3)x2 + 0x5 = 6 - 6 + 0 = 0. A zero dot product means cos(theta) = 0, so the vectors are orthogonal (90 degrees): in attention terms this query-key pair contributes a score of zero, with no similarity in the dot-product sense.

## Lesson 2 — Matrices: Transformations, Layers & Why Shapes Matter

Your code runs X @ W with X of shape (32, 512) and W stored as (256, 512), and it crashes. What is wrong and what is the fix? :: The inner dimensions must match: 512 vs 256 does not. W is stored (out x in), so use X @ W.T: (32x512)(512x256) = (32x256). The inner 512 is consumed by the dot products; the outer 32 and 256 set the result shape.

A layer has W = [[0.5,-1,2],[1,0.5,-0.5]], b = (-1,-2) and input x = (2,1,4). What is y = Wx + b, and how many learnable numbers does the layer have? :: Wx = (0.5*2 - 1*1 + 2*4, 1*2 + 0.5*1 - 0.5*4) = (8, 0.5); adding b gives y = (7, -1.5). Parameters: 2x3 weights + 2 biases = 8.

You stack ten layers y = Wx + b with no activation between them and training gives no better results than one layer. Why? :: Matrices multiply into one: W2(W1x + b1) + b2 = (W2W1)x + (W2b1 + b2). Example: W1=[[1,2],[0,1]], W2=[[3,0],[1,1]], x=(1,-1) gives (-3,-2) layer by layer and with W2W1=[[3,6],[1,3]]. A ReLU between them gives (0,0) instead, so the collapse is broken. Add non-linearities.

A teammate calls inv(A) to solve A x = b for a 4096x4096 system, and results are noisy. What would you change and why? :: Use a solver (solve or lstsq) instead of forming the inverse. Inversion costs about n^3 operations (4096^3 is about 68.7 billion), is fragile when the matrix is nearly singular (determinant near 0, redundant columns), and does not exist at all for non-square or rank-deficient matrices.

## Lesson 3 — Eigenvectors, SVD & Low-Rank Structure: PCA and LoRA

A team's matrix A sends the vector (1, 1) to (3, 3) and (1, -1) to (1, -1). What are the eigenvectors and eigenvalues, and what does this say about how A treats those directions? :: (1, 1) is an eigenvector with eigenvalue 3 and (1, -1) is an eigenvector with eigenvalue 1, because A v = lambda v holds for both. Along those two directions A does no rotating at all, it only multiplies by 3 and by 1, so the matrix reduces to two independent stretches. Check: eigenvalues sum to the trace (3 + 1 = 4) and multiply to the determinant (3 x 1 = 3).

You compute the SVD of a feature matrix and get squared singular values 81, 16, 4, 1. Your manager wants to store only the top 2 directions. What do you tell them about what is lost? :: Keeping the top 2 retains (81 + 16) / 102 = 97/102, about 95.1% of the matrix's squared size. The error is the square root of the discarded squared values, sqrt(4 + 1) = sqrt(5), about 2.24, and Eckart-Young says no other rank-2 matrix gets closer. The reasoning only holds because the values fall off fast; with a flat spread, truncating would cost real accuracy.

You run PCA on a dataset's features but forget to subtract the mean first. What goes wrong with the 'main axis' you get back, and how do you fix it? :: The top eigenvector tends to point toward the mean, so it describes where the data sits instead of how it spreads. Fix: center every feature (subtract its mean) before building the covariance matrix. In the lesson's five-point cloud, the centered covariance [2.5 2.0; 2.0 2.5] has eigenvalues 4.5 and 0.5, so the main axis (1,1)/sqrt(2) holds 4.5/5 = 90% of the variance.

A team wants to fine-tune a 4096 x 4096 weight matrix but cannot afford to train all of it. They use LoRA with rank r = 8. How many numbers do they train for that matrix, how does it compare to a full update, and what is the risk? :: They train B (4096 x 8) and A (8 x 4096): 32,768 + 32,768 = 65,536 numbers, versus 4096 x 4096 = 16,777,216 for a full update. That is 256 times fewer, about 0.39%. The risk is that the needed change is not low-rank: BA has rank at most 8, so if the task needs many directions, a small r underfits and they should compare a few ranks on held-out data.

## Lesson 4 — Derivatives: Slope, Rate of Change & the Functions ML Uses

A training loop reports a loss of 0.36 and a derivative of -1.2 with respect to the model's prediction (squared error, target 1.0, prediction 0.4). Which way should the prediction move, and how do you know? :: Up. The derivative is negative, so increasing the prediction lowers the loss. Check: d/dŷ of (y-ŷ)² = -2(y-ŷ) = -2(0.6) = -1.2, and at ŷ = 0.7 the loss falls to 0.09. A positive derivative would mean move the other way.

You estimate the slope of f(x) = x² at x = 3 using (f(3+h) - f(3))/h and get 7 with h = 1, 6.1 with h = 0.1, 6.01 with h = 0.01. What number is it heading toward, and why can't you just set h = 0? :: Toward 6, the derivative 2x at x = 3. Algebraically the slope is exactly 6 + h, so it approaches 6 as h shrinks. Setting h = 0 gives 0/0, which is undefined; the derivative is the limit, not the value at h = 0.

A network with sigmoid units stops learning in its early layers. Using the sigmoid derivative σ' = σ(1-σ), what is the largest slope any sigmoid unit can pass back, and what happens when you multiply it across three layers? :: The slope peaks at 0.25 (at input 0, where σ = 0.5) and is near 0 in the tails (0.0177 at input ±4). Three layers at the maximum give 0.25 × 0.25 × 0.25 = 0.0156, so the learning signal shrinks fast. This is the vanishing-signal problem behind many switches to ReLU.

You hand-derive the gradient of -ln σ(z) and your code returns -0.8808 at z = 2. A central-difference check with h = 1e-5 returns -0.1192. What is the likely bug and what should the formula be? :: The formula has a bug: relative error is |-0.8808 - (-0.1192)|/0.1192 ≈ 6.4, far above the ~1e-6 a correct gradient would show. The correct derivative is σ(z) - 1 = 0.8808 - 1 = -0.1192; the code returned -σ(z), dropping the 1. Use the check on a small model only, since it costs an extra evaluation per parameter.

## Lesson 5 — Partial Derivatives & Gradients: Which Way Is Downhill?

A team's loss is L(w1,w2) = (w1-3)^2 + 4(w2-1)^2 and the weights sit at (0, 3). Which weight does the gradient say to change harder, and what is the gradient? :: Gradient = (dL/dw1, dL/dw2) = (2(0-3), 8(3-1)) = (-6, 16). The w2 entry is larger in size (16 vs 6), so the loss is steeper along w2. Each partial holds the other weight fixed. Its length is sqrt(36+256) = about 17.09, the steepness of the climb.

With gradient (-6, 16), learning rate 0.1 and start (0, 3), what are the weights after one step and why does that step lower the loss? :: w <- w - eta*grad = (0 - 0.1*(-6), 3 - 0.1*16) = (0.6, 1.4). The loss falls from 25 to (0.6-3)^2 + 4(0.4)^2 = 5.76 + 0.64 = 6.4. Stepping against the gradient is the steepest way down: the rate along a unit direction u is |grad|*cos(theta), which is most negative when u points opposite the gradient.

Training loss goes 25, 32.8, 61.7, 120.5 on the toy loss with learning rate 0.3. What is going wrong, and what would you change? :: The learning rate is too large for the steep direction. The w2 error is multiplied by (1 - 0.3*8) = -1.4 each step, so it flips sign and grows. Lower the rate: below 0.25 the factor 1 - 8*eta has size under 1 and the error shrinks (eta = 0.1 gives loss 25 -> 6.4 -> 3.712 -> 2.36). The learning rate is a hyperparameter you choose, not something training learns.

Why is the gradient from one mini-batch not the same as the true gradient, and what happens to the error as the batch gets bigger? :: A mini-batch gradient averages the per-example gradients of a small random sample, so it is an unbiased but noisy estimate of the full-data gradient. With per-example gradients -8,-2,-6,-4,-10,0 the true value is -5, but batches of 2 give anywhere from -9 to -1. The noise shrinks roughly like 1/sqrt(batch size), the same behavior as a sample mean, so quadrupling the batch only halves it.

## Lesson 6 — The Chain Rule & Backpropagation: How Gradients Flow Through a Network

A team trains a 10-layer sigmoid network and finds the first layer's weights barely change while the last layer learns fine. What is happening, with numbers? :: Vanishing gradients. The backward pass multiplies one factor per layer, and the sigmoid slope is at most 0.25. Even in the best case with weights of 1, ten layers scale the gradient by 0.25^10 = about 0.00000095, so the first layer gets under a millionth of the signal. Fixes: ReLU-style activations, residual connections, careful initialization.

In the tiny network (x=1.5, w1=0.4, b1=0.1, w2=0.9, b2=-0.2, target 1.0), you hand-derive dL/dw1 = -0.1792. How do you check it before trusting it, and what should you see? :: Use the M4 finite-difference check: nudge w1 by +/-0.000001, recompute the loss, and divide the difference by 0.000002. You should get -0.1792 again. The hand path is (-0.5986)(0.9)(0.2217)(1.5) = -0.1792: dL/dy-hat, times w2, times the sigmoid slope h(1-h), times x.

A variable x feeds two nodes u = x+1 and v = x^2, and f = u*v. At x=3 a teammate multiplies the two path gradients and gets 216. What went wrong and what is the right answer? :: Gradients along different paths are added, not multiplied; within one path they multiply. Path through u: v * 1 = 9. Path through v: u * 2x = 4 * 6 = 24. Total df/dx = 9 + 24 = 33, which matches f = x^3 + x^2 giving 3x^2 + 2x = 33. This is also why a residual connection gives dy/dx = 1 + dF/dx.

A model has 50 million parameters and one scalar loss. Why does training use reverse mode (backprop) instead of forward mode, and what does it cost you? :: Forward mode needs one sweep per parameter, about 50 million sweeps. Reverse mode starts from the single loss and multiplies a vector by one Jacobian at a time, so one backward pass gives every gradient, at a small multiple of the forward cost. The cost is memory: the forward values must be saved until the backward pass uses them, which is why training needs far more memory than inference.

## Lesson 7 — Integrals: Accumulation, Area & the Continuous Side of Probability

A team models the gap between requests as f(t) = 2e^(−2t) per second. A teammate asks for the chance the next request arrives between 0.5 s and 1.5 s. What do you compute, and what do you get? :: Probability over a range is an area under the density, so integrate f from 0.5 to 1.5. Using the CDF F(t) = 1 − e^(−2t): F(1.5) − F(0.5) = e^(−1) − e^(−3) = 0.3679 − 0.0498 = 0.318. Subtracting density heights f(1.5) − f(0.5) would be wrong, because a density height is not a probability.

You approximate ∫ x² dx from 0 to 3 with six left-edge rectangles and get 6.875, but your colleague says the answer is 9. Who is right, and what would you change? :: Colleague: the exact value is F(3) − F(0) = 27/3 − 0 = 9 by the fundamental theorem, with antiderivative x³/3. The left-edge sum undershoots because x² is rising, so each rectangle misses the sliver above it. More slices fix it: 100 left slices give 8.865, 1,000 give 8.987, and midpoints reach 9.000 quickly.

A Monte Carlo estimate of an integral with 1,000 samples has a typical error of 0.006, which is too noisy. A teammate says to run 2,000 samples to halve it. What is wrong, and what should you do? :: Monte Carlo error shrinks like σ/√n, so doubling the samples only cuts the error by 1/√2, about 29%. To halve it you need 4× the samples, so 4,000 for roughly 0.003. Ten times less error would need 100× the samples, so also look for a way to reduce σ itself.

Raw model scores (logits) of 2, 1 and 0 are positive after exponentiating, but they sum to 11.107, not 1. What do you do to get usable probabilities, and what are they? :: Divide each exponentiated score by their sum, the normalizing constant: e²/11.107 = 0.665, e¹/11.107 = 0.245, e⁰/11.107 = 0.090, which add to 1. The continuous analogue is dividing e^(−x²/2) by its integral √(2π) ≈ 2.5066 to get the Gaussian density.

