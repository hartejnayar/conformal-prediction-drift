# Research Question Selection: Decision Record

**Project:** *Efficiency vs. Reliability: A Comparative Analysis of Conformal Prediction Methods Under Covariate and Concept Drift*
**Repository:** `conformal-prediction-drift`
**Status:** Selection decision — provisional until the verification gate is cleared.
**Related files:** `literature_matrix.md`, `research_gap.md`, `rq_assessment.md`

---

## 1. Decision

**Selected research question: RQ2 — Recovery after an abrupt change.**

**Working question:** *After a known abrupt change in the data-generating process, how quickly do selected online conformal prediction methods return to their target local coverage, and what interval-width cost do they incur during recovery?*

**Decision type:** Provisional selection, contingent on the verification gate in §4. RQ1 is retained as a co-primary *design* (its matched-schedule benchmark is the experimental vehicle), but RQ2 supplies the **primary finding and contribution claim**. RQ3 is retained only as a robustness subsection.

---

## 2. Why RQ2 is the strongest evidence-supported option

### 2.1 It is anchored in an explicit, verified critique already in the literature

- **Vaze (2026)** argues that **signed long-run coverage error can conceal sustained miscoverage** and motivates time-resolved rather than aggregate metrics. This is a direct, citable motivation for measuring behavior *after* a change rather than averaging over the whole horizon. `[VERIFY: exact theorem statement and empirical scope in Vaze preprint]`
- **Bhatnagar et al. (2023)** explicitly motivate **interval-wise / strongly adaptive** performance because whole-horizon regret can hide poor behavior over short segments. Recovery time is the natural observable counterpart of that motivation. `[VERIFY: exact baseline list, metrics table, and whether recovery-like metrics are already reported]`
- **Gibbs & Candès (2024), DtACI** provide direct qualitative evidence that lag after change is a real, method-dependent phenomenon: your notes record that AgACI "lags behind" DtACI when shift magnitude jumps, and that this lag is visible in the average step-size trajectories (Fig. 4.3, right panels). This is **[EXPERIMENT]** evidence that the recovery question is empirically live, not hypothetical.

**Why this matters for selection:** RQ2 is not invented from a gap in a table; it is the **measurable form of an existing critique** (Vaze) and an **existing empirical observation** (DtACI's jump-shift lag). That is the strongest kind of support a candidate RQ can have at this stage.

### 2.2 The measurable outcome is well-defined and directly comparable

Recovery time has a clean operational definition that can be pre-registered:

- **Recovery time** \(T_{\text{rec}}\) = first \(t\) after the change point such that rolling coverage stays within a tolerance band \([1-\alpha-\epsilon,\;1-\alpha+\epsilon]\) for \(m\) consecutive steps.
- **Cumulative miscoverage after change** = area between the rolling-coverage curve and \(1-\alpha\) over the recovery window.
- **Width cost during recovery** = mean interval width in the recovery window minus steady-state width.
- **Steady-state width after recovery** = checks over-correction.

Each is a scalar (or small vector) per method, per change magnitude, per seed — directly comparable across methods and directly interpretable ("method X takes 40 vs. 120 steps; width penalty 15% vs. 30%"). This is stronger than RQ1's multi-metric trade-off, which risks "efficiency" collapsing width, runtime, and fit count without justification (flagged in `literature_matrix.md` and `rq_assessment.md` §RQ1).

### 2.3 It has the cleanest contribution shape

RQ2 supports a contribution of the form: **a pre-registered metric + a comparative finding + a design implication.** The DtACI evidence already predicts a direction (adaptivity lag under abrupt change), and Vaze's signed-vs-absolute critique supplies the theoretical framing. The contribution is therefore not "we ran methods on metrics" (the risk flagged for RQ1) but "we formalize and measure a phenomenon the theory and prior experiments jointly predict."

### 2.4 Feasibility is high and the design risk is controllable

- Synthetic streams with known change points are cheap and controllable.
- Real-data change points (2008 crisis; COVID waves) are available from the DtACI pipelines as a secondary track.
- The main risk — sensitivity of \(T_{\text{rec}}\) to the tolerance band and window size — is a **design** risk, addressed by pre-registering the band/window and reporting sensitivity in an appendix. It is not a feasibility blocker.

---

## 3. Why each competing option was rejected as the *primary* question

### 3.1 RQ1 — Local reliability and interval efficiency under controlled drift

**Decision: co-primary design, not primary contribution claim.**

**Reasons for rejection as primary:**
1. **Novelty is contingent on an unverified gate.** RQ1's core claim — a matched cross-family benchmark with unified coverage and width metrics — only survives if Bhatnagar et al. (2023) does **not** already include DtACI/AgACI as baselines with set-size reporting, and if Vaze (2026) does **not** already report local coverage + width on a matched schedule. `[VERIFY]` Until those are checked, RQ1's novelty is a hypothesis, not a fact.
2. **The premise is already partly present.** Srinivas (2026) and Vaze (2026) explicitly frame coverage–efficiency trade-offs; Bhatnagar et al. already report coverage *and* prediction-set size under shift. "Jointly studying coverage and set size" is **not** novel (`research_gap.md` §2.3–2.5; `rq_assessment.md` §RQ1). The narrow surviving claim is the *matched cross-family* benchmark, which is a benchmark contribution, not a finding-driven one.
3. **Contribution clarity is weaker.** Without a sharp non-obvious finding (e.g., rank reversal between gradual and abrupt regimes), RQ1 risks reading as "we ran existing methods on existing metrics" — exactly the risk flagged in `rq_assessment.md`.
4. **Efficiency is under-specified.** Width, runtime, and fit count are three different dimensions; collapsing them into one score is unjustified (`literature_matrix.md`, cross-paper synthesis point 4). RQ1 would have to solve that definitional problem before it can claim a trade-off finding.

**Retained role:** RQ1's matched-schedule design (same base predictor, same drift taxonomy, same metrics, repeated seeds) is the **experimental vehicle** for RQ2. The drift taxonomy (stationary / gradual / abrupt with controlled magnitude) is the substrate on which recovery is measured. RQ1 is thus **not discarded** — it is demoted from "contribution claim" to "experimental design."

### 3.2 RQ3 — Sensitivity and computational cost under matched conditions

**Decision: rejected as primary; retained as a robustness subsection.**

**Reasons for rejection:**
1. **Low novelty as a theme.** Hyperparameter sensitivity ablations are standard; DtACI already discusses \(\eta,\sigma\) and reports variable-\(\eta\) appendix results; JAWS-X already treats computation (fit count, \(K\)) as part of efficiency (`rq_assessment.md` §RQ3). "Does tuning matter?" has an unsurprising answer.
2. **Contribution is engineering, not scientific, unless it reveals a non-obvious trade-off.** A matched cost accounting is useful but modest; it is strongest as a *practical guidance note*, not a standalone paper.
3. **Measurement noise.** Wall-clock is hardware/software-dependent and noisy; fit count is cleaner but less practically interpretable. Results may be dominated by implementation details rather than algorithmic properties.
4. **Risk of becoming an ablation appendix.** Without a sharp finding, RQ3 reads as a robustness check, not a contribution.

**Retained role:** Folded into RQ2 as a subsection: does the recovery-time finding survive across reasonable tuning choices, and what does adaptation cost computationally? This strengthens RQ2's main claim rather than competing with it.

### 3.3 Other options considered and rejected

- **"Efficiency vs. reliability" as a standalone comparison (the original working title).** Rejected because the broad premise is already present in Srinivas (2026), Vaze (2026), and Bhatnagar et al. (2023) — see `research_gap.md` §2.3–2.5 and `rq_assessment.md` cross-cutting §3. Too broad to be a defensible contribution claim.
- **Covariate/feedback shift track (JAWS-X family) merged with online drift methods in one leaderboard.** Rejected as incoherent: JAWS-X's SCS/FCS assumptions and jackknife+-style workflow are not comparable to online threshold adaptation without violating the method's assumptions (`research_gap.md` §6; `rq_assessment.md` §RQ1 feasibility risk). Kept as a separate track or excluded with explicit rationale.
- **A pure theory extension (e.g., sharper local-coverage bounds).** Rejected for this project: the theoretical machinery (dynamic regret, strongly adaptive regret) is already developed in Bhatnagar, Srinivas, and Vaze; a theory contribution would require expertise and scope beyond the current project's comparative/empirical aim.

---

## 4. Verification gate — must be cleared before RQ2 is locked in

RQ2's superiority is conditional on the same unverified facts that constrain RQ1. Clear these **before** finalizing the selection:

| # | Verification task | Source to check | Why it matters for RQ2 |
|---|---|---|---|
| V1 | Does Bhatnagar et al. (2023) already define/report a recovery-like or interval-wise metric? | Full PDF, metrics table, experiments section | If yes, RQ2's metric is not new; narrow to a different regime or a different definition. |
| V2 | What exactly does Vaze (2026) replicate, and does it report time-resolved coverage? | Preprint, experiments section | If it already reports post-change behavior, RQ2 must narrow to a setting it does not cover. |
| V3 | Do time-series CP papers already define recovery-time-like metrics? (Stankeviciute 2021; Lin 2022; Xu & Xie) | Full texts / abstracts | These are the most likely prior art for "adaptation/recovery time." |
| V4 | Does DtACI's repo provide drift generators and baseline implementations reusable for RQ2? | `github.com/isgibbs/DtACI` | Determines implementation cost, not novelty, but affects feasibility timeline. |
| V5 | Forward-citation check on DtACI, Bhatnagar, Zaffran — any post-2024 paper already doing matched-schedule recovery comparison? | Google Scholar / Semantic Scholar, queries logged | Detects late-breaking overlap. |

**Decision rule:** If V1–V3 show that recovery-time metrics already exist *and are applied to the ACI/DtACI family under controlled schedules*, RQ2's novelty narrows to "a different drift taxonomy" or "a different method set" — reassess before committing. If they show the metric exists only in a different setting (e.g., time-series CP without ACI-family comparison), RQ2 remains viable as a focused empirical contribution.

---

## 5. Selected contribution claim (draft, provisional)

> *We formalize and pre-register a recovery-time metric for online conformal prediction under abrupt distribution change, and use a matched-schedule benchmark (shared base predictor, controlled drift taxonomy, repeated seeds) to show how selected online methods differ in recovery speed, cumulative post-change miscoverage, and interval-width cost. The finding is anchored in the signed-vs-absolute coverage critique of Vaze (2026) and the interval-wise guarantees of Bhatnagar et al. (2023), and is directly motivated by the observed step-size lag of AgACI relative to DtACI under magnitude jumps (Gibbs & Candès, 2024, Fig. 4.3).*

**Evidence labels for this claim:**
- *Formalize and pre-register a recovery-time metric* — **[READER INFERENCE / CONTRIBUTION CLAIM]**, conditional on V1–V3.
- *Matched-schedule benchmark* — **[DESIGN]**, derived from `research_gap.md` §4 and `rq_assessment.md` §RQ1.
- *Signed-vs-absolute critique* — **[THEOREM / AUTHOR FRAMING]**, cite Vaze (2026) after verification.
- *Interval-wise guarantees* — **[THEOREM]**, cite Bhatnagar et al. (2023) after verification.
- *Observed lag* — **[EXPERIMENT]**, Gibbs & Candès (2024), Fig. 4.3.

---

## 6. Immediate next actions

1. **Clear V1–V3** (Bhatnagar full text; Vaze preprint; time-series CP papers). Record findings in `search_log.md` with dates and exact queries.
2. **If RQ2 survives:** update `research_gap.md` §5 to mark RQ2 as the selected question and RQ1/RQ3 as retained design/robustness components.
3. **If RQ2 fails V1–V3:** fall back to RQ1 as primary — but only after the Bhatnagar/Vaze baseline-list check, and with the drift taxonomy designed to expose a rank reversal.
4. **Update `literature_matrix.md`** with any new `[VERIFY]` resolutions and add the recovery-metric prior-art row once V1–V3 are cleared.
5. **Do not write "novel" or "gap" in any draft** until the verification gate is cleared and logged.

---

## 7. Decision summary

| Option | Decision | Rationale (one line) |
|---|---|---|
| **RQ2 (recovery after abrupt change)** | **Selected (provisional)** | Measurable form of an existing critique (Vaze) plus an existing empirical observation (DtACI lag); cleanest contribution shape; high feasibility. |
| RQ1 (local reliability + width) | Retained as experimental design | Novelty contingent on unverified Bhatnagar/Vaze gate; broader premise already present; weaker contribution clarity. |
| RQ3 (sensitivity + cost) | Retained as robustness subsection | Low novelty as theme; engineering contribution; noisy measurement. |
| Broad "efficiency vs. reliability" framing | Rejected as standalone claim | Already present in Srinivas/Vaze/Bhatnagar; too broad. |
| Merged covariate-shift + online-drift leaderboard | Rejected | Assumption incompatibility (SCS/FCS vs. online threshold adaptation). |
| Pure theory extension | Rejected for this project | Requires scope/expertise beyond comparative/empirical aim; theory already developed. |

**Binding constraint:** novelty verification (V1–V3), not feasibility or measurement. RQ2 is the strongest *evidence-supported* option; it becomes the strongest *defensible* option only once the verification gate is cleared and logged.