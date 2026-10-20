# Research Gap and Candidate Research Questions

**Project:** *Efficiency vs. Reliability: A Comparative Analysis of Conformal Prediction Methods Under Covariate and Concept Drift*  
**Repository:** `conformal-prediction-drift`  
**Status:** Working synthesis — the research gap is provisional and must be confirmed by full-text review and a reproducible search.

## 1. Purpose and scope

This note separates what the selected papers establish mathematically from what they report empirically. These are different kinds of evidence:

- **Theoretical guarantee:** a formal statement proved under specified assumptions, feedback conditions, metrics, and comparator/benchmark.
- **Experimental result:** an observed result on the authors' chosen simulations or datasets, under their chosen baselines, metrics, and implementation.
- **Interpretation / open question:** a synthesis or hypothesis suggested by the papers, not a result proved by them.

Do not compare guarantees as if they were interchangeable when one concerns marginal or long-run coverage, another concerns regret on time intervals, and another concerns interval length or computational cost. Likewise, do not treat results on different datasets and metrics as a direct head-to-head ranking.

## 2. Theoretical guarantees versus empirical evidence

### 2.1 Gibbs & Candès (2024) — DtACI

**Reference:** Isaac Gibbs and Emmanuel J. Candès, “Conformal Inference for Online Prediction with Arbitrary Distribution Shifts,” *Journal of Machine Learning Research*, 25(162), 1–36 (2024).  
Official source: https://jmlr.org/beta/papers/v25/22-1218.html

**Theoretical guarantees (paper-level summary)**
- Proposes Dynamic Adaptive Conformal Inference (DtACI), which adapts the step size in the ACI update over time.
- Establishes a regret guarantee over local time intervals of a chosen width; the paper's framing is that this helps the method adapt to changing distributions without requiring the shift rate to be known in advance.
- The guarantee is not the same as a promise of perfect coverage at every time point. Record the precise theorem, comparator, and conditions from the theorem statements before using formal wording in the paper.

**Empirical results**
- Evaluates online prediction under simulated distribution changes, including stationary, smooth-changing, and abrupt/regime-change settings, as described in the working reading notes.
- Real-data evaluations include stock-market volatility prediction (Nvidia, AMD, Blackberry, and Fannie Mae) and COVID-19 case-count prediction for four counties.
- The reported experiments support the claim that the method can adapt in these evaluated settings. They do not establish that DtACI is universally best, or that it is computationally more efficient than all methods.

**Implication for this project**
- Strong source for online adaptation and local-time behavior.
- A matched comparison should measure both local coverage behavior and interval efficiency, rather than assuming a regret guarantee alone answers both questions.
- The exact theorem assumptions, interval width, baseline configuration, and metrics should be transcribed from the full paper before final publication.

### 2.2 Prinster, Saria & Liu (2023) — JAWS-X

**Reference:** Drew Prinster, Suchi Saria, and Anqi Liu, “JAWS-X: Addressing Efficiency Bottlenecks of Conformal Prediction Under Standard and Feedback Covariate Shift,” *Proceedings of ICML 2023*, PMLR 202:28167–28190.  
Official source: https://proceedings.mlr.press/v202/prinster23a.html

**Theoretical guarantees**
- JAW-FCS extends a finite-sample coverage guarantee of jackknife+ to feedback covariate shift under the paper's stated assumptions.
- The paper proposes computational relaxations, including JAW-KLOO and WCV+, and states finite-sample guarantees for those relaxations. The guarantees differ in strength and conditions; use the exact theorem statements rather than summarizing them as one identical bound.
- These are guarantees for the paper's covariate-shift/feedback setting, not a general guarantee for arbitrary concept drift.

**Empirical results**
- Evaluates the methods on real-world tasks including biomolecular design and active learning, with datasets also described in the reading notes (including airfoil self-noise, red-wine quality, superconductivity, and communities-and-crime).
- Reports comparisons using predictive interval coverage and width, variability, predictor/model performance, and computational workload such as the number of model fits.
- The authors report that JAWS-X methods offer a practical balance of statistical and computational efficiency and outperform selected state-of-the-art baselines on their evaluated tasks.

**Implication for this project**
- A useful source for measuring interval width and computation, not only coverage.
- It should not be directly ranked against DtACI without acknowledging the different problem setup: covariate/feedback shift and jackknife+-style methods versus online adaptive threshold methods.
- Clarify whether a common experiment can legitimately include both families, or whether they require separate experimental tracks.

### 2.3 Bhatnagar et al. (2023) — Strongly adaptive online conformal prediction

**Reference:** Aadyot Bhatnagar, Huan Wang, Caiming Xiong, and Yu Bai, “Improved Online Conformal Prediction via Strongly Adaptive Online Learning,” *Proceedings of ICML 2023*, PMLR 202:2337–2363.  
Official source: https://proceedings.mlr.press/v202/bhatnagar23a.html

**Theoretical guarantees**
- Develops online conformal methods based on strongly adaptive regret, which assesses performance over intervals/subsequences rather than only over the full horizon.
- The authors prove near-optimal strongly adaptive regret across interval lengths and approximately valid coverage.
- These results concern the paper's regret and coverage formulations. They should not be rewritten as a universal finite-sample exact-coverage guarantee.

**Empirical results**
- The paper reports experiments on real-world tasks, including time-series forecasting and image classification under distribution shift.
- The authors report better coverage and smaller prediction sets than the baselines they tested.
- Those findings are experimental and depend on the paper's chosen tasks, methods, metrics, and baselines; they do not alone establish superiority over DtACI or JAWS-X under a shared protocol.

**Implication for this project**
- Supports measuring performance locally around drift events, not just averages over the entire test sequence.
- Offers a relevant baseline family if the project focuses on online methods and interval-level adaptation.

### 2.4 Srinivas (2026) — Online conformal prediction with efficiency guarantees

**Reference:** Vaidehi Srinivas, “Online Conformal Prediction with Efficiency Guarantees,” *Proceedings of SODA 2026*, pp. 6696–6726.  
Official source: https://epubs.siam.org/doi/abs/10.1137/1.9781611978971.240  
Preprint: https://arxiv.org/abs/2507.02496

**Theoretical guarantees**
- Defines an online setting in which an algorithm outputs an interval before observing the next outcome, seeking both a target coverage fraction and low average interval length.
- For exchangeable/random-order sequences, the abstract states a construction with coverage \((1-\alpha)-o(1)\) and average length bounded by the best fixed interval achieving coverage in hindsight.
- For arbitrary sequences, the paper establishes a trade-off: a method approximating the best fixed interval's average length must incur a larger number of coverage mistakes, with the factor depending on the efficiency approximation and problem aspect ratio. It gives an algorithm matching the Pareto-optimal trade-off described in the abstract.
- The algorithm is stated to be deterministic and robust to an adaptive adversary.

**Empirical results**
- The official abstract presents theoretical and algorithmic results; it does not, by itself, establish an empirical benchmark against DtACI, JAWS-X, or Bhatnagar et al.
- Treat the paper primarily as a formal framing of coverage/interval-length trade-offs unless a full-text section provides relevant experiments.

**Implication for this project**
- This paper means the broad idea of “jointly studying coverage and interval efficiency” is already present in the literature.
- It also highlights that exchangeable and arbitrary-sequence settings have materially different achievable trade-offs. A new experiment must specify which setting it evaluates.

### 2.5 Vaze (2026) — Simultaneous coverage and efficiency guarantees

**Reference:** Rahul Vaze, “Simultaneous Coverage and Efficiency Guarantee in Online Conformal Prediction,” arXiv preprint, posted 29 July 2026.  
Source: https://arxiv.org/abs/2607.26577

**Theoretical guarantees (preprint abstract)**
- Argues that signed long-run coverage error can conceal sustained miscoverage, and that efficiency guarantees against a fixed hindsight benchmark may be unsuitable when the optimal threshold changes.
- Proposes a unified framework for simultaneous control of absolute/non-cancelling coverage violation and efficiency against a changing benchmark.
- The abstract reports results for fully adversarial, stochastic with full-score feedback, and covariate-dependent stochastic settings, including a sliding-window quantile tracker and a partitioned ACI method.
- This is an arXiv preprint in the source checked for this note. Do not describe it as peer-reviewed unless publication status is separately verified.

**Empirical results**
- The preprint's accessible summary includes numerical/real-data evaluation material, including a volatility benchmark and adversarial examples. These are supporting experiments, distinct from the theoretical claims above.
- Before including detailed numerical claims, extract the exact experimental setup, metrics, baselines, and results from the preprint itself.

**Implication for this project**
- It directly overlaps the broad coverage-plus-efficiency theme and must be discussed before a gap is asserted.
- A potentially defensible empirical contribution would need to differ in a specific way, such as a reproducible matched benchmark, a defined drift taxonomy, robustness/sensitivity analysis, or computational accounting not already supplied by this work.

## 3. Cross-paper synthesis

| Paper | Main setting | Theoretical emphasis | Empirical evidence identified so far | Key comparability caution |
|---|---|---|---|---|
| Gibbs & Candès (2024), DtACI | Online prediction with changing distributions | Local-interval regret and adaptive step-size selection | Simulations; stock volatility; COVID case counts | Local regret/coverage behavior is not equivalent to width/runtime efficiency |
| Prinster et al. (2023), JAWS-X | Standard and feedback covariate shift | Finite-sample coverage results for jackknife+-based methods under stated assumptions | Biomolecular design, active learning, UCI-style regression tasks; width and computation | Different shift assumptions and model-fitting workflow from online threshold adaptation |
| Bhatnagar et al. (2023) | Arbitrarily changing online environments | Strongly adaptive regret and approximately valid coverage | Time-series forecasting and image classification | Regret, coverage, and prediction-set size definitions must be aligned |
| Srinivas (2026) | Exchangeable and arbitrary sequences | Formal Pareto trade-off between coverage mistakes and average interval length | No comparative benchmark established from the official abstract alone | The online interval game may differ from predictor-based drift benchmarks |
| Vaze (2026 preprint) | Adversarial, stochastic, and covariate-dependent online settings | Simultaneous coverage and efficiency against changing benchmarks | Numerical/real-data material indicated in the preprint summary; details require extraction | New preprint overlaps the proposed theme; publication status and protocol need careful verification |

### Synthesis

1. **Theoretical guarantees are heterogeneous.** They concern different quantities—coverage, signed versus absolute coverage error, regret over intervals, interval length, and computational cost—and rely on different assumptions and feedback models.
2. **The empirical evidence is fragmented across setups.** The selected papers use different datasets, predictors, shift mechanisms, baselines, and evaluation metrics. Results cannot be treated as a common leaderboard.
3. **The broad research premise is not novel on its own.** Several papers already consider coverage alongside efficiency or set size, and the 2026 papers make this especially explicit.
4. **A plausible research opportunity is empirical comparability, not simply combining two metrics.** A controlled benchmark could ask how a carefully selected set of methods behaves under the same synthetic drift schedules, common base predictors, consistent metrics, and repeated random seeds. However, the exact novelty of such a benchmark must be checked against full-text work, code repositories, and related papers.
5. **Method selection needs a feasibility filter.** JAWS-X may not fit directly into the same online prediction protocol as DtACI/Bhatnagar-style methods. If assumptions or feedback protocols cannot be aligned, compare methods in separate tracks and explain why.

## 4. Provisional research gap

### Working gap statement

The reviewed papers provide formal guarantees and empirical evaluations for online conformal prediction, but their guarantees and experiments are not directly interchangeable: they target different shift regimes, feedback assumptions, coverage/regret notions, and efficiency measures. A carefully scoped empirical study may therefore be useful if it evaluates selected compatible online methods under a shared set of controlled drift scenarios and reports local coverage, interval efficiency, adaptation after drift, and computation with consistent definitions.

**This is a provisional gap, not a novelty claim.** Before committing, inspect the full texts and available code for Bhatnagar et al. and Vaze in particular, then search for existing comparative studies/benchmarks that already use matched drift scenarios and these metrics. If an equivalent benchmark exists, narrow or change the question rather than claiming it is missing.

### What this gap does *not* claim

- It does not claim that prior work ignores interval length or prediction-set size.
- It does not claim that prior work lacks theoretical coverage/efficiency guarantees.
- It does not claim that DtACI, JAWS-X, Bhatnagar et al., Srinivas, and Vaze can all be compared fairly in one protocol without adaptation.
- It does not claim novelty until the related-work search is complete.

## 5. Candidate research questions

The following are evidence-motivated candidates. They are not yet approved final RQs; each needs a targeted novelty check.

### RQ1 — Local reliability and interval efficiency under controlled drift

**Question:** Under a common online regression setup, how do selected online conformal prediction methods trade off empirical coverage and normalized interval width under stationary data, gradual covariate drift, and abrupt distribution changes?

**Why the literature motivates it**
- DtACI studies changing distributions and local-interval regret, with simulation and real-data evaluations.
- Bhatnagar et al. target strongly adaptive performance across time intervals.
- Srinivas and Vaze show that coverage and efficiency are coupled objectives, so reporting coverage alone is insufficient.

**Possible operationalization**
- Choose a small, compatible set of online methods (for example, ACI, DtACI, and a strongly adaptive method from Bhatnagar et al.).
- Hold the base predictor, target miscoverage level, data stream, and evaluation horizon fixed.
- Generate stationary, gradual-drift, and abrupt-drift streams with multiple random seeds.
- Report empirical coverage, mean/median interval width, normalized width, and rolling/local coverage.
- Report confidence intervals across seeds and make the full protocol reproducible.

**Novelty check required:** Search for existing papers/code that already run these same methods on matched stationary, gradual, and abrupt drift schedules while reporting both local coverage and interval width. This question is the most straightforward empirical option if a sufficiently distinct benchmark can be established.

### RQ2 — Recovery after an abrupt change

**Question:** After a known abrupt change in the data-generating process, how quickly do selected online conformal methods return to their target local coverage, and what interval-width cost do they incur during recovery?

**Why the literature motivates it**
- DtACI is motivated by the need to adapt to changing dynamics and is evaluated on abrupt/regime-change simulations in the reading notes.
- Bhatnagar et al. motivate interval-level guarantees because whole-horizon performance can hide poor performance on shorter segments.
- Vaze highlights that signed long-run error may conceal persistent one-sided miscoverage; this supports examining time-resolved rather than only aggregate coverage.

**Possible operationalization**
- Define a change point and a pre-registered tolerance band around target coverage.
- Define “recovery time” before running experiments (for example, the first time rolling coverage enters and remains within the tolerance band for a fixed number of steps).
- Measure recovery time, cumulative miscoverage after the change, interval width during recovery, and steady-state width after recovery.
- Vary drift magnitude and change-point position; repeat over seeds.

**Novelty check required:** Verify whether the selected papers already define and compare recovery-time metrics. Do not call the metric itself novel unless the literature search supports that claim.

### RQ3 — Sensitivity and computational cost under matched conditions

**Question:** How sensitive are selected online conformal methods to their tuning choices, and how does that sensitivity affect the empirical coverage–width trade-off and computational cost under the same drift scenarios?

**Why the literature motivates it**
- DtACI adapts an ACI step-size parameter over time, making adaptation and tuning relevant to performance.
- JAWS-X explicitly treats computation as part of efficiency, including computational relaxations and model-fit workload, although its covariate-shift setting may require a separate track.
- Bhatnagar et al. and Vaze provide alternative online adaptation frameworks whose practical behavior should not be inferred from theorem statements alone.

**Possible operationalization**
- Select compatible online methods and define a small, principled tuning grid.
- Measure sensitivity of coverage, width, and recovery time to hyperparameters.
- Measure wall-clock time, memory if practical, and number of predictor/model fits; state hardware/software versions.
- Keep tuning budgets comparable and separate training/tuning time from per-example inference/update time.

**Novelty check required:** Check existing ablations and benchmark tables carefully. If the papers already perform a comparable sensitivity and cost analysis, use the result to refine the RQ rather than repeating it.

## 6. Recommended direction (provisional)

Start with **RQ1 as the main question**, and use **RQ2 as a focused secondary analysis** if the experimental budget permits. RQ3 may expand the project too much unless computational accounting is central to the intended contribution.

A feasible first paper could evaluate three compatible online methods under three synthetic regimes, use one fixed base predictor and repeated seeds, and report coverage, interval width, and post-change recovery. Do not include JAWS-X in the same primary comparison unless its assumptions and evaluation protocol can be made meaningfully comparable; otherwise report it as a separate covariate-shift track or exclude it with an explicit rationale.

## 7. Next verification tasks

- [ ] Read theorem statements and assumptions directly from the full texts, especially the exact coverage/regret quantity and comparator.
- [ ] Extract exact datasets, shift construction, baselines, and metrics from Bhatnagar et al. and Vaze (2026).
- [ ] Check the full Srinivas paper for any experiments beyond the official abstract.
- [ ] Search for existing comparative benchmarks using matched drift schedules and local coverage/interval-width metrics.
- [ ] Check official code repositories and record software dependencies and reproducibility limitations.
- [ ] Choose one primary RQ only after the novelty check.
- [ ] Update `literature_matrix.csv` so theoretical guarantees and experimental results have separate fields.

## 8. Sources checked

1. Gibbs, I. & Candès, E. J. (2024). *Conformal Inference for Online Prediction with Arbitrary Distribution Shifts*. JMLR 25(162), 1–36. https://jmlr.org/beta/papers/v25/22-1218.html
2. Prinster, D., Saria, S. & Liu, A. (2023). *JAWS-X: Addressing Efficiency Bottlenecks of Conformal Prediction Under Standard and Feedback Covariate Shift*. ICML / PMLR 202:28167–28190. https://proceedings.mlr.press/v202/prinster23a.html
3. Bhatnagar, A., Wang, H., Xiong, C. & Bai, Y. (2023). *Improved Online Conformal Prediction via Strongly Adaptive Online Learning*. ICML / PMLR 202:2337–2363. https://proceedings.mlr.press/v202/bhatnagar23a.html
4. Srinivas, V. (2026). *Online Conformal Prediction with Efficiency Guarantees*. SODA 2026, pp. 6696–6726. https://epubs.siam.org/doi/abs/10.1137/1.9781611978971.240
5. Vaze, R. (2026). *Simultaneous Coverage and Efficiency Guarantee in Online Conformal Prediction*. arXiv:2607.26577 (preprint). https://arxiv.org/abs/2607.26577
