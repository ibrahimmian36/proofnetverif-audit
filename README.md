# ProofNetVerif Faithfulness Audit

We checked the valid split of ProofNetVerif and found 10 reference Lean 4 formalizations that do not say what their informal statements say, and 33 rows labeled correct whose predicted formalization is wrong. 9 of the 10 reference defects and 30 of the 33 label errors come with an explicit witness; the rest were verified by hand.

## What we checked

- Upstream: [PAug/ProofNetVerif](https://huggingface.co/datasets/PAug/ProofNetVerif) on Hugging Face, revision [`91183e5`](https://huggingface.co/datasets/PAug/ProofNetVerif/tree/91183e5b12d64374827bf2782db629b5b0f8f319) (`91183e5b12d64374827bf2782db629b5b0f8f319`, the latest commit, dated 2025-03-24). Our copy was taken on 2026-07-24, and every record quoted here matches this revision.
- Scope: all 2,300 rows of the `valid` split. Each row pairs an informal statement (`nl_statement`), a reference formalization (`lean4_formalization`) and a predicted formalization (`lean4_prediction`) with a `correct` label, which the dataset card defines as the prediction being semantically equivalent to the informal statement (and the reference). We asked two questions: does each reference say what the informal statement says, and is each `correct` label right? We did not check proofs.
- Method: an automated screen proposed candidate defects; every finding listed here was confirmed by hand. Findings marked counterexample-backed come with an explicit witness that makes the formal statement false (or true) where the informal one is not.
- Row numbers are 0-based positions in the `valid` split. The upstream `id` names the exercise and is shared by every prediction for that exercise, so a row number is needed to identify a prediction.

This is a list of confirmed defects, not a certificate for the rest of the benchmark. A reference or label that does not appear here has not been shown to be right.

## Findings

[`findings.jsonl`](findings.jsonl) has one row per finding: 10 with `"kind": "reference"` and 33 with `"kind": "label"`. Each row carries short verbatim excerpts of the informal statement and of the Lean fragment at fault.

### Reference formalizations

A defective reference affects every prediction for its exercise: a faithful prediction disagrees with it, and a prediction that copies the defect agrees with it. These 10 references are shared by 104 of the 2,300 rows; the second column gives the count per exercise.

| Id | Rows | What is wrong | Evidence |
|---|---|---|---|
| `Axler\|exercise_4_4` | 9 | Disjoint is applied to two root counts in ℕ, where it means that the smaller count is 0; it does not say that p and p' share no root. Witness: p = X^2 + 1: it has degree 2 and two distinct roots (i and -i), and p' = 2X has only the root 0, so the informal statement holds; but Disjoint 1 2 is false in ℕ, so the Lean iff fails. | counterexample-backed |
| `Axler\|exercise_6_7` | 4 | Mathlib's inner product is conjugate-linear in the first argument, while the textbook's is linear in the first argument, so the right side equals ⟪v, u⟫_ℂ, not ⟪u, v⟫_ℂ. Witness: V = ℂ, u = 1, v = i: the left side is conj(1) * i = i, the right side is (2 - 2 + i * 0 - i * 4) / 4 = -i. | counterexample-backed |
| `Dummit-Foote\|exercise_1_1_5` | 8 | The Lean says no group structure of any kind exists on the type ZMod n; the exercise is about the multiplication of residue classes. Witness: For every n, the additive group of ZMod n written multiplicatively (mul := +, one := 0, inv := neg) is a Group (ZMod n). | counterexample-backed |
| `Dummit-Foote\|exercise_1_6_4` | 9 | Multiplicative ℝ is the additive group (ℝ, +) written multiplicatively, not ℝ - {0}. The additive groups of ℝ and ℂ are isomorphic (both are ℚ-vector spaces of the same dimension), so the Lean statement is false. The intended groups are ℝˣ and ℂˣ. | hand-verified |
| `Herstein\|exercise_4_3_25` | 21 | Mathlib's Ideal of a noncommutative ring is a left ideal; the exercise is about two-sided ideals, and 2 x 2 real matrices have proper nonzero left ideals. Witness: The matrices whose second column is zero form a left ideal that is neither ⊥ nor ⊤. | counterexample-backed |
| `Ireland-Rosen\|exercise_4_5` | 14 | IsPrimitiveRoot a p means that a has multiplicative order p, which no element of ZMod p has; a primitive root modulo p has order p - 1. The left side is false for every a. Witness: p = 3, t = 0, a = 2: 2 is a primitive root modulo 3 and -2 = 1 has order 1 = (3 - 1)/2, so the right side holds, but 2^3 = 2 ≠ 1 in ZMod 3, so the left side is false. | counterexample-backed |
| `Rudin\|exercise_2_24` | 18 | Every point of A lies in closure A, so the hypothesis holds in every metric space and the Lean claims that every metric space is separable. A limit point needs x in the closure of A \ {x}. Witness: An uncountable set with the discrete metric satisfies the hypothesis but is not separable. | counterexample-backed |
| `Rudin\|exercise_5_2` | 4 | For f : ℝ → ℝ, f⁻¹ is the pointwise reciprocal x ↦ (f x)⁻¹, not the inverse function. Witness: a = 0, b = 1, f x = x + 1: then g y = 1/(y + 1), and at x = 1/2 we get deriv g (f x) = -4/25 while 1 / deriv f x = 1. | counterexample-backed |
| `Munkres\|exercise_23_4` | 11 | CofiniteTopology X is a type synonym for X, not a class. The file header turns off Lean's binder check, so the bracket binder only supplies an element of X; the topology on X stays arbitrary, and the Lean claims that every infinite subset of any space is connected. Witness: X = ℕ with the discrete topology, s = univ: s is infinite and not connected. | counterexample-backed |
| `Munkres\|exercise_24_2` | 6 | The unit sphere in ℝ is the two-point set {-1, 1}, not the circle. Witness: f(1) = 0, f(-1) = 1 is continuous on the discrete set {-1, 1}, and f(x) ≠ f(-x) at both points. | counterexample-backed |

The finding marked hand-verified has no explicit witness: the isomorphism between the additive groups of ℝ and ℂ exists (it is a ℚ-linear bijection, which needs the axiom of choice) but is not given by a formula.

### Labels

Each row below is labeled `correct = true`, but its prediction does not say what the informal statement says, so the label should be `false`. They cover 22 exercises. In 8 of the 33 rows (from 6 exercises) the prediction repeats the defect of its reference; the label then matches the reference but not the informal statement, and fixing only the reference would not fix the label.

| Id | Row | What is wrong | Evidence |
|---|---|---|---|
| `Axler\|exercise_1_3` | 1759 | No vector space appears: v is an auto-bound variable with no module structure (current Lean elaborates it as an integer), so the statement says nothing about vectors in V. | hand-verified |
| `Axler\|exercise_3_1` | 2062 | The field is weakened to a division ring; the conclusion needs commutativity. Witness: K = V = ℍ (quaternions, dimension 1 over itself), f x = x * i: f is K-linear, but no single a gives f x = a * x (x = 1 forces a = i, and then x = j fails). | counterexample-backed |
| `Axler\|exercise_5_12` | 1419 | The field is weakened to a division ring; the step from 'each vector has its own eigenvalue' to 'one scalar for all vectors' needs commutativity. Witness: K = V = ℍ, T x = x * i: each nonzero x satisfies T x = (x i x⁻¹) • x, but no single a works for both x = 1 and x = j. | counterexample-backed |
| `Axler\|exercise_6_16` | 2015 | Submodule.annihilator is the ideal of scalars that kill U, not the orthogonal complement; no inner product is assumed. Witness: K = ℝ, V = ℝ², U = span{(1, 0)}: only the scalar 0 kills U, so the annihilator is ⊥, but U ≠ ⊤. | counterexample-backed |
| `Dummit-Foote\|exercise_1_1_5` | 1605 | Same defect as the reference: it denies every group structure on the type ZMod n, not the one given by multiplication of residue classes. Witness: The additive group of ZMod n written multiplicatively is a Group (ZMod n). | counterexample-backed |
| `Dummit-Foote\|exercise_1_6_4` | 1078 | Same defect as the reference: Multiplicative ℝ is (ℝ, +), and the additive groups of ℝ and ℂ are isomorphic. | hand-verified |
| `Dummit-Foote\|exercise_3_2_11` | 809 | The subgroupOf arguments are swapped: under H ≤ K, K.subgroupOf H is all of H, with index 1, so the formula says \|G:H\| = \|G:K\|. Witness: G cyclic of order 4, H trivial, K of order 2: \|G:H\| = 4, but the right side is 2 * 1 = 2. | counterexample-backed |
| `Dummit-Foote\|exercise_3_2_11` | 1788 | Same as row 809: the subgroupOf arguments are swapped, so the formula says \|G:H\| = \|G:K\|. Witness: G cyclic of order 4, H trivial, K of order 2: \|G:H\| = 4, but the right side is 2 * 1 = 2. | counterexample-backed |
| `Herstein\|exercise_4_3_25` | 994 | Same defect as the reference: Ideal is a left ideal here, and the exercise is about two-sided ideals. Witness: The matrices whose second column is zero form a left ideal that is neither ⊥ nor ⊤. | counterexample-backed |
| `Herstein\|exercise_4_3_25` | 1953 | Same defect as the reference: Ideal is a left ideal here, and the exercise is about two-sided ideals. Witness: The matrices whose second column is zero form a left ideal that is neither ⊥ nor ⊤. | counterexample-backed |
| `Herstein\|exercise_5_2_20` | 669 | The supremum of subspaces is the span of their union, not the set-theoretic union. Witness: V = F², t = the two coordinate axes: both are proper and their supremum is ⊤, although their union misses (1, 1). | counterexample-backed |
| `Herstein\|exercise_5_2_20` | 2202 | The supremum of subspaces is the span of their union, not the set-theoretic union. Witness: V = K², t = the two coordinate axes: both are proper and their supremum is ⊤, although their union misses (1, 1). | counterexample-backed |
| `Ireland-Rosen\|exercise_4_5` | 941 | IsPrimitiveRoot a p is stated in ℤ and with order p instead of p - 1 (the reference has the same order error), so the left side is false for every integer a. Witness: p = 3, a = 2: 2 is a primitive root modulo 3 and -2 = 1 has order 1 = (3 - 1)/2 in ZMod 3, but IsPrimitiveRoot 2 3 is false in ℤ. | counterexample-backed |
| `Ireland-Rosen\|exercise_4_5` | 968 | The hypothesis makes 4p + 3 prime instead of p, and the right side drops the negation of a. Witness: p = 4 (4p + 3 = 19 is prime), a = 1: orderOf 1 = 1 = (4 - 1)/2, so the right side holds, but 1 is not a primitive 4th root of unity. | counterexample-backed |
| `Pugh\|exercise_2_12a` | 953 | The one-directional statement is written as an iff; the converse is false for injective f that is not surjective. Witness: p n = 0 for even n and 1 for odd n, f k = 2k, l = 0: p ∘ f tends to 0, but p does not converge. | counterexample-backed |
| `Pugh\|exercise_2_12a` | 1771 | The one-directional statement is written as an iff; the converse is false for injective f that is not surjective. Witness: α = ℝ, l = 𝓝 0, p n = 0 for even n and 1 for odd n, f k = 2k: p ∘ f tends to 0, but p does not. | counterexample-backed |
| `Pugh\|exercise_2_12a` | 1999 | The one-directional statement is written as an iff; the converse is false for injective f that is not surjective. Witness: p n = 0 for even n and 1 for odd n, f k = 2k, l = 0: p ∘ f tends to 0, but p does not converge. | counterexample-backed |
| `Pugh\|exercise_2_41` | 1527 | Positive definiteness (norm x = 0 only for x = 0) is omitted, so the hypotheses describe a seminorm. Witness: m = 1, norm ≡ 0: all hypotheses hold and the set {x \| norm x ≤ 1} is all of ℝ¹, which is not compact. | counterexample-backed |
| `Pugh\|exercise_3_4` | 742 | n elaborates as a natural number, so sqrt is Nat.sqrt (the floor of the square root) and the limit is taken in the discrete topology on ℕ. Witness: For n = k² - 1 with k ≥ 1, (n + 1).sqrt - n.sqrt = k - (k - 1) = 1, so the sequence is 1 infinitely often and does not tend to 0. | counterexample-backed |
| `Pugh\|exercise_3_4` | 1576 | The lambda binds m but the body uses the fixed outer n, so the sequence is constant. Witness: n = 0: the sequence is constantly √1 - √0 = 1, which does not tend to 0. | counterexample-backed |
| `Putnam\|exercise_2017_b3` | 704 | The hypothesis is computed in ℕ, where 2 / 3 = 0 and 3 / 2 = 1, so it only says c 0 = 1. Witness: c i = 1 for all i: the hypothesis holds, and the conclusion's sum (computed in ℝ) is 1 + 1/2 + 1/4 + ... = 2, which is rational. | counterexample-backed |
| `Rudin\|exercise_1_18a` | 201 | Every coordinate product is required to vanish instead of the dot product. Witness: k = 2, x = (1, 1): the condition forces y = 0, so no y ≠ 0 exists, while y = (1, -1) works for the dot product. | counterexample-backed |
| `Rudin\|exercise_1_18a` | 2245 | Every coordinate product is required to vanish instead of the dot product. Witness: k = 2, x = (1, 1): the condition forces y = 0, so no y ≠ 0 exists, while y = (1, -1) works for the dot product. | counterexample-backed |
| `Rudin\|exercise_2_24` | 71 | Same defect as the reference: every point of S lies in closure S, so the hypothesis holds in every metric space. Witness: An uncountable set with the discrete metric satisfies the hypothesis but is not separable. | counterexample-backed |
| `Rudin\|exercise_2_24` | 2075 | Same defect as the reference: every point of S lies in closure S, so the hypothesis holds in every metric space. Witness: An uncountable set with the discrete metric satisfies the hypothesis but has no countable dense subset. | counterexample-backed |
| `Rudin\|exercise_3_3` | 404 | The initial value is 2 instead of √2, which contradicts the conclusion s n < 2. Witness: n = 0: the hypothesis gives s 0 = 2, so s 0 < 2 fails. | counterexample-backed |
| `Rudin\|exercise_4_5a` | 894 | The extension must agree with f on all of ℝ instead of on E. Witness: E = {0}, f = 0 on (-∞, 0] and 1 on (0, ∞): f is continuous on E, but no continuous g equals f everywhere. | counterexample-backed |
| `Rudin\|exercise_4_6` | 934 | f.graph '' E is not the graph of f over E: in the mathlib of the dataset's era Function.graph f is a curried relation X → ℝ → Prop, so its image of E is a set of predicates, not a set of points of X × ℝ (current mathlib rejects the expression). | hand-verified |
| `Rudin\|exercise_5_2` | 294 | Inv.inv f is the pointwise reciprocal x ↦ (f x)⁻¹, not the inverse function (the reference has the same error). Witness: a = 0, b = 1, f x = x + 1: at x = 1/2, deriv (Inv.inv f) (f x) = -4/25, while (deriv f x)⁻¹ = 1. | counterexample-backed |
| `Shakarchi\|exercise_1_13a` | 1138 | The conclusion ranges over all of ℂ instead of U, and the constant is pinned to f 0 without assuming 0 ∈ U. Witness: U = the open unit disc, f = 0 on the closed unit disc and 1 outside: the hypotheses hold, but f 2 ≠ f 0. | counterexample-backed |
| `Shakarchi\|exercise_3_22` | 417 | Continuity on the closed disc is weakened to continuity of the restriction to the circle, which no longer links f inside the disc to its boundary values. Witness: f = 0 on the open disc and f z = 1/z elsewhere: every hypothesis holds, so the Lean statement (which concludes False) is false. | counterexample-backed |
| `Shakarchi\|exercise_3_22` | 918 | Continuity on the closed disc is weakened to continuity of the restriction to the circle, which no longer links f inside the disc to its boundary values. Witness: f = 0 on the open disc and f z = 1/z elsewhere: every hypothesis holds, so the Lean statement (which concludes False) is false. | counterexample-backed |
| `Shakarchi\|exercise_3_22` | 1339 | Continuity on the closed disc is weakened to continuity of the restriction to the circle, which no longer links f inside the disc to its boundary values. Witness: f = 0 on the open disc and f z = 1/z elsewhere: every hypothesis holds, so the Lean statement (which concludes False) is false. | counterexample-backed |

The three label findings marked hand-verified have no explicit witness. In `Axler|exercise_1_3` row 1759 and `Rudin|exercise_4_6` row 934 the prediction is about the wrong objects (no vector space appears; the set is not the graph of f), so there is no single instance to exhibit; in `Dummit-Foote|exercise_1_6_4` row 1078 the refuting isomorphism exists but is not given by a formula.

### Not included

We list label errors only in one direction: rows labeled correct whose prediction we confirmed to be wrong. We also found rows labeled incorrect whose prediction we consider acceptable, but most of those turn on how closely a prediction must follow the reference (for example, a sound generalization of the book's hypotheses), which is a question of annotation policy rather than a clear error, so we do not list them. Candidate defects that we could not confirm, or whose stated mechanism did not hold up when we elaborated the statement in Lean, are also left out.

### Fields in findings.jsonl

- `kind`: `reference` (a defect in `lean4_formalization`) or `label` (a wrong `correct` label).
- `id`: the upstream exercise id.
- `rows` (reference findings): the row numbers of every prediction that shares this reference.
- `row` (label findings): the row number of the prediction.
- `published_label`, `proposed_label` (label findings): the upstream label and the label we propose.
- `shares_reference_defect` (label findings): whether the prediction repeats a defect of its reference that is listed above.
- `informal_excerpt`: the phrase of the informal statement at issue, quoted verbatim.
- `lean_excerpt`: the Lean fragment at fault, quoted verbatim (`...` marks an omission).
- `defect`: what is wrong, in one or two sentences.
- `witness`: the explicit witness, or `null` if there is none.
- `evidence`: `counterexample-backed` or `hand-verified`.

## License

Our text and findings are licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) (see [`LICENSE`](LICENSE)). This repository contains no code.

The short excerpts of ProofNetVerif rows quoted here are from [PAug/ProofNetVerif](https://huggingface.co/datasets/PAug/ProofNetVerif), which is distributed under the MIT license. This repository does not contain a copy of the dataset. ProofNetVerif was introduced in [Improving Autoformalization using Type Checking](https://arxiv.org/abs/2406.07222).

## Citation

Ibrahim Mian and Shayaan Siddique, Millennium Research. *ProofNetVerif Faithfulness Audit*, 2026. https://github.com/ibrahimmian36/proofnetverif-audit

Machine-readable citation metadata is in [`CITATION.cff`](CITATION.cff). Contact: ibrahimnmian@gmail.com.
