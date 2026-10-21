# Assessment of RQ1–RQ3: Novelty, Feasibility, Measurable Outcomes, and Contribution Support

Below I assess each candidate research question against four criteria: **novelty** (is it already covered?), **feasibility** (can you actually run it with available resources and time?), **measurable outcomes** (are the outputs well-defined and comparable?), and **contribution clarity** (can it support a defensible research contribution?). I use your existing evidence-quality labels and flag every claim that still requires verification as **[VERIFY]**.

A summary table precedes the detailed assessments.

---

## Summary comparison

| Criterion | RQ1 (Local reliability + width under controlled drift) | RQ2 (Recovery after abrupt change) | RQ3 (Sensitivity + computational cost) |
|---|---|---|---|
| **Novelty** | Moderate–low: broad premise already present; narrow matched-schedule benchmark may survive **[VERIFY]** | Moderate: recovery-time metric is less standard, but may exist in time-series CP work **[VERIFY]** | Low–moderate: sensitivity ablations are common; cross-method cost accounting under matched drift is less common |
| **Feasibility** | High: DtACI repo + synthetic drift generators; ACI/DtACI/strongly-adaptive methods are implementable | High–moderate: needs pre-registered recovery-time definition and sufficient post-change horizon | Moderate: requires careful hardware/software logging and comparable tuning budgets |
| **Measurable outcomes** | Strong: coverage, width, normalized width, rolling coverage are standard and comparable | Strong if recovery-time is defined *a priori*; risk of metric cherry-picking otherwise | Moderate–strong: wall-clock, fit count, and sensitivity curves are measurable but noisy across hardware |
| **Contribution clarity** | Moderate: could be a "benchmark/empirical characterization" paper if narrow enough | Strong: a focused metric + finding ("method X recovers in Y steps; width cost Z") is a clean contribution | Weak–moderate: risks being an ablation appendix rather than a contribution |
| **Overall recommendation** | **Primary candidate**, conditional on narrowing after verification | **Strong secondary / fallback** if RQ1 is already covered | **Supplementary only** unless computation is central |

---

## RQ1 — Local reliability and interval efficiency under controlled drift

**Question:** Under a common online regression setup, how do selected online conformal prediction methods trade off empirical coverage and normalized interval width under stationary data, gradual covariate drift, and abrupt distribution changes?

### Novelty — Moderate to low, narrowing required

**What is already present:**
- Srinivas (2026) and Vaze (2026) explicitly frame coverage–efficiency trade-offs theoretically. Your `research_gap.md` §2.4–2.5 correctly records this.
- Bhatnagar et al. (2023) already report coverage *and* prediction-set size under distribution shift — so "jointly studying coverage and set size" is **not** novel.
- DtACI already compares ACI variants under stationary/smooth/abrupt simulations with local coverage metrics.

**Where novelty may survive:**
- A **matched cross-family benchmark** — same base predictor, same drift schedules, same metrics — across ACI/DtACI, a strongly adaptive method (Bhatnagar), and possibly a sliding-window tracker (Vaze-style). Your five-paper set does not appear to contain this **[VERIFY by reading Bhatnagar full text and Vaze preprint]**.
- A **reproducible drift taxonomy** (stationary / gradual / abrupt with controlled magnitude and frequency) reported alongside coverage and width. This is an engineering/scientific contribution if no existing benchmark defines it explicitly.

**Critical verification gate:** If Bhatnagar et al. already includes DtACI/AgACI as baselines *and* reports set size, RQ1's novelty collapses to "yet another benchmark." Before committing, extract the baseline list and metrics table from Bhatnagar's full PDF. Similarly, check whether Vaze's preprint replication already reports local coverage + width on a matched schedule.

### Feasibility — High

- **Data:** Synthetic drift streams can be generated cheaply (Gaussian mean shifts, covariate drift on UCI regression, or the DtACI stock/COVID pipelines if you want a real-data track). No proprietary data required.
- **Methods:** DtACI code is confirmed available (`isgibbs/DtACI`). ACI is a few lines. Strongly adaptive methods from Bhatnagar require **[VERIFY]** code availability; if unavailable, re-implementation is nontrivial but tractable for a single method.
- **Compute:** Modest — small base predictors (linear/ridge/GARCH, or a small MLP) and a few thousand timesteps per run; multiple seeds are cheap.
- **Time:** A focused RQ1 study is realistic within a semester-scale project.
- **Risk:** Method-family comparability. JAWS-X cannot be dropped into the same protocol as DtACI without violating SCS/FCS assumptions — keep it as a separate track or exclude with explicit rationale (your `research_gap.md` §6 already says this).

### Measurable outcomes — Strong

Well-defined and standard:
- Empirical coverage vs. target \(1-\alpha\) (with binomial confidence intervals across seeds).
- Mean and median interval width; **normalized width** (width divided by a scale estimate) to make datasets comparable.
- Rolling/local coverage over windows of a pre-specified size.
- Optional: absolute coverage violation (Vaze's non-cancelling metric) in addition to signed coverage.
- All metrics computed per drift regime and per method, with multiple seeds and reported variability.

**Risk:** "Efficiency" collapsing width, runtime, and fit count into one score without justification. Your `literature_matrix.md` already flags this. Keep dimensions separate.

### Contribution clarity — Moderate

A clean contribution would be: *"A reproducible matched-schedule benchmark showing that method family A trades width for local coverage stability differently from family B under abrupt vs. gradual drift."* That is a legitimate empirical/benchmark contribution **if** the novelty check confirms no equivalent benchmark exists.

**Weakness:** Without a sharp finding (e.g., a consistent reversal of rankings between gradual and abrupt regimes), the paper risks reading as "we ran existing methods on existing metrics." The contribution is strongest when the drift taxonomy is **designed to expose a trade-off the theory predicts** (e.g., Srinivas's Pareto trade-off between exchangeable and arbitrary sequences; Vaze's signed-vs-absolute coverage distinction).

### Verdict on RQ1
**Primary candidate.** Proceed **only after** the Bhatnagar/Vaze verification gate. If the gate fails, narrow to RQ2.

---

## RQ2 — Recovery after an abrupt change

**Question:** After a known abrupt change in the data-generating process, how quickly do selected online conformal methods return to their target local coverage, and what interval-width cost do they incur during recovery?

### Novelty — Moderate

- **What is already present:** Bhatnagar's strongly adaptive regret is explicitly interval-wise, which is conceptually adjacent to "recovery." DtACI's jump-shift simulations qualitatively show lag behavior (your notes: AgACI lags after magnitude jumps). Vaze's critique of signed long-run error concealing sustained miscoverage motivates time-resolved metrics.
- **Where novelty may survive:** A **pre-registered, formally defined recovery-time metric** (e.g., first \(t\) after change point such that rolling coverage stays within a tolerance band for \(m\) consecutive steps), applied consistently across methods and drift magnitudes. If no paper defines and compares recovery time this way **[VERIFY]**, that is a clean, narrow methodological contribution.
- **Risk:** Time-series CP papers (Stankeviciute et al. 2021; Lin et al. 2022; Xu & Xie) may already define adaptation/recovery-like metrics. **Check these specifically** — they are the most likely prior art for recovery-time definitions.

### Feasibility — High to moderate

- **Design:** Requires a pre-registered change-point protocol: change-point location, magnitude grid, post-change horizon length, tolerance band, and consecutive-step requirement. All controllable in simulation.
- **Data:** Synthetic drift streams with known change points are trivial to generate; real-data change points (e.g., 2008 crisis in stock data, COVID waves) are available but less controlled.
- **Compute:** Same order as RQ1 — modest.
- **Risk:** Recovery-time estimates are sensitive to the tolerance band and window size. This is a **design risk**, not a feasibility risk — mitigate by pre-registering and reporting sensitivity to the band/window.

### Measurable outcomes — Strong (if pre-registered)

- **Recovery time** (steps to re-enter and stay within tolerance) — primary.
- **Cumulative miscoverage after change** (area between coverage curve and target during the recovery window) — captures severity, not just speed.
- **Interval-width cost during recovery** (mean width in the recovery window minus steady-state width) — captures the price of adaptation.
- **Steady-state width after recovery** — checks whether the method over-corrects.
- All reported per change magnitude and per method, with seeds and confidence intervals.

**Strength:** These outcomes are directly comparable across methods and directly interpretable ("method X takes 40 steps vs. 120 steps to recover; width penalty is 15% vs. 30%").

### Contribution clarity — Strong

RQ2 has the cleanest contribution shape: *a defined metric + a comparative finding + a design implication* (e.g., "tuning for steady-state width degrades recovery time; the Pareto frontier is non-trivial"). It also connects naturally to Vaze's signed-vs-absolute coverage critique and Bhatnagar's interval-wise guarantees, giving theoretical anchoring.

**Weakness:** If a time-series CP paper already defines recovery time, novelty narrows to "applied to online ACI-family methods under controlled schedules" — still publishable as a focused empirical study, but weaker.

### Verdict on RQ2
**Strong secondary / fallback.** Best choice if RQ1's novelty gate fails. Also a natural **secondary analysis within RQ1's paper** if budget allows — report recovery time as a focused subsection rather than a separate paper.

---

## RQ3 — Sensitivity and computational cost under matched conditions

**Question:** How sensitive are selected online conformal methods to their tuning choices, and how does that sensitivity affect the empirical coverage–width trade-off and computational cost under the same drift scenarios?

### Novelty — Low to moderate

- **What is already present:** Hyperparameter sensitivity ablations are standard in ML papers; DtACI already discusses \(\eta,\sigma\) choices and reports variable-\(\eta\) appendix results. JAWS-X explicitly treats computation (fit count, \(K\) selection) as part of efficiency. So "sensitivity + cost" as a *theme* is well covered.
- **Where novelty may survive:** A **matched cross-method cost accounting** — same hardware, same software stack, same tuning budget — comparing wall-clock, fit count, and memory for ACI/DtACI vs. strongly adaptive methods. This is rarely done rigorously and could be a useful practical contribution. But it is an **engineering contribution**, not a scientific one, unless it reveals a non-obvious trade-off.

### Feasibility — Moderate

- **Compute:** Wall-clock measurements require controlled hardware/software environments; cross-machine reproducibility is difficult. Budget time for repeated timing runs and variance reporting.
- **Tuning grids:** Must be comparable across methods (same budget, same search protocol) or the comparison is unfair — this is a design burden.
- **Risk:** Results may be dominated by implementation details (vectorization, caching) rather than algorithmic properties, weakening the scientific claim.

### Measurable outcomes — Moderate to strong

- Sensitivity curves: coverage/width/recovery-time as a function of each hyperparameter, per method.
- Wall-clock time per update and per prediction (with hardware/software versions stated).
- Number of predictor/model fits.
- Memory footprint if practical.
- **Risk:** Wall-clock is noisy; fit count is cleaner but less practically interpretable.

### Contribution clarity — Weak to moderate

RQ3 risks becoming an **ablation appendix** rather than a standalone contribution. It is strongest as:
- A **subsection within RQ1 or RQ2**, showing that the main comparative finding is robust to tuning choices and quantifying the cost of adaptation.
- A **practical guidance note** ("for practitioners with budget \(B\), method X is preferable") — useful but modest.

It is weakest as a standalone paper because the scientific question ("does tuning matter?") has an unsurprising answer ("yes, somewhat").

### Verdict on RQ3
**Supplementary only.** Fold into RQ1 or RQ2 as a robustness/cost subsection unless computation is the central intended contribution of the project.

---

## Cross-cutting assessment

### 1. The verification gate dominates all three RQs

All three RQs depend on the same unverified facts:
- **[VERIFY]** Bhatnagar et al. (2023) baseline list and metrics — determines whether RQ1 survives.
- **[VERIFY]** Vaze (2026) preprint replication scope — determines whether RQ1/RQ2 survive.
- **[VERIFY]** Time-series CP papers (Stankeviciute 2021; Lin 2022; Xu & Xie) — determine whether recovery-time metrics already exist (RQ2).

**Recommendation:** Do these three verifications **before** committing to any RQ. They are cheap (one to three hours of full-text reading) and could save weeks of work on a covered question.

### 2. Contribution type differs by RQ

- **RQ1** → *benchmark/empirical characterization* contribution. Defensible if the benchmark is genuinely matched and the finding is non-obvious (e.g., rank reversal across drift regimes).
- **RQ2** → *metric + comparative finding* contribution. Cleanest scientific shape; strongest if the metric is new or applied in a new setting.
- **RQ3** → *engineering/practical guidance* contribution. Weakest as standalone.

### 3. Feasibility is high across the board — the binding constraint is novelty, not resources

All three are runnable with the DtACI repo, synthetic drift generators, and modest compute. The project's risk is **not** "can I run it?" but "has someone already run it?" The literature search (A.3 queries in the previous answer) and the verification gate above are the critical path.

### 4. Recommended sequencing

1. **Week 1:** Run the verification gate (Bhatnagar full text; Vaze preprint; time-series CP papers; forward-citation check on DtACI).
2. **Week 2:** If RQ1 survives → commit to RQ1 as primary, RQ2 as secondary subsection, RQ3 as robustness appendix.
3. **If RQ1 fails** (Bhatnagar or Vaze already covers it) → commit to RQ2 as primary, with RQ3 folded in.
4. **Throughout:** Maintain `search_log.md` and update the `[VERIFY]` labels in `literature_matrix.md` as facts are confirmed.

### 5. What would make each RQ clearly contribution-worthy

| RQ | Sufficient condition for a clear contribution |
|---|---|
| **RQ1** | No existing matched cross-family benchmark + a finding of rank reversal or trade-off reversal between gradual and abrupt regimes. |
| **RQ2** | A pre-registered recovery-time metric not already used in this method family + a finding that recovery speed and steady-state width are non-trivially coupled (or decoupled) across methods. |
| **RQ3** | A matched cost accounting that reveals a practical recommendation not already implied by the papers' own ablations (e.g., a method that is cheaper *and* more adaptive under a specific regime). |

---

## Bottom line

- **RQ1 is the primary candidate** — feasible, strongly measurable, and potentially contribution-worthy — **but its novelty is contingent on the Bhatnagar/Vaze verification gate.** Without that check, the risk of "already covered" is real.
- **RQ2 is the strongest fallback and the cleanest contribution shape** — a well-defined metric plus a comparative finding. It should probably appear as a subsection of RQ1's paper regardless.
- **RQ3 is supplementary** — fold it in as a robustness/cost analysis rather than treating it as a standalone contribution.

The binding constraint across all three is **novelty verification**, not feasibility or measurement. Prioritize the verification gate before committing.