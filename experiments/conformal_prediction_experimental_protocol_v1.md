# Project Apex Research — Experimental Protocol v1.0
**Status:** Day 1 and Day 2 finalized for pilot implementation  
**Working title:** *Efficiency vs. Reliability: A Comparative Analysis of Conformal Prediction Methods Under Covariate and Concept Drift*

## 1. Research question

After a known abrupt change in the data-generating process, how quickly do selected online conformal prediction methods return to their target local coverage, and what interval-width cost do they incur during recovery?

This protocol defines a reproducible initial simulation study. It does **not** establish that the proposed research gap is novel; that claim remains subject to mentor feedback and a broader literature check.

## 2. Hypotheses

- **H1 — Recovery differences:** Selected online conformal prediction methods will exhibit different recovery times following abrupt distribution shifts.
- **H2 — Efficiency trade-off:** Faster coverage recovery may come at the cost of greater prediction-interval width during recovery.
- **H3 — Shift dependence:** Recovery time and width cost will vary with shift type and magnitude.
- **H4 — No universal winner assumed:** Relative performance may differ across scenarios.

These are testable hypotheses, not expected results or conclusions.

## 3. Scope and experimental sequence

**Initial scope:** controlled synthetic experiments only.

1. Implement and unit-test the metrics.
2. Run a minimal end-to-end pilot with one scenario and one method.
3. Audit method compatibility and run a small pilot across candidate methods.
4. Freeze the main experimental settings before full runs.
5. Consider a real-world dataset only after the simulation results and code are reproducible and interpretable.

The first experiment should prioritize correctness and traceability over the number of methods or scenarios.

## 4. Evaluation target and local coverage

- **Nominal target coverage:** 90%.
- **Initial local coverage window:** 100 observations.
- **Initial acceptable local-coverage band:** 85%–95%.
- **Recovery persistence requirement:** 50 consecutive local-coverage estimates within the band.

For coverage indicators \(I_t = 1\) when the observed outcome is contained in its prediction interval and \(0\) otherwise, trailing-window empirical coverage is

\[
\widehat C_t = \frac{1}{100}\sum_{i=t-99}^{t} I_i.
\]

Coverage should be computed from outcomes and intervals generated in the online evaluation stream, using the same timing convention for all methods.

### Recovery-time definition

Let \(t_0\) be the known change point. Define recovery time as

\[
T_{\mathrm{rec}} = t_r - t_0,
\]

where \(t_r\) is the earliest eligible time at which the rolling empirical coverage is within [0.85, 0.95] for 50 consecutive eligible estimates.

**Window-mixing rule:** do not start checking recovery until the 100-observation coverage window contains only post-change observations. This avoids labeling a method recovered because pre-change observations are still contributing to the rolling estimate.

**Non-recovery:** if the criterion is not met before the evaluation horizon, mark the run as “not recovered within horizon.” Do not silently drop it or assign an arbitrary recovery time. Report the non-recovery fraction and account for right-censoring when summarizing recovery time.

**Important interpretation caveat:** rolling empirical coverage is noisy and discrete. With a window of 100, coverage changes in increments of 0.01; the selected tolerance band is a practical operational criterion, not proof that the method's true conditional coverage has returned to 90%.

## 5. Interval-width cost

Let \(w_t\) be the prediction-interval width at time \(t\). Define a fixed pre-change baseline window of 100 evaluation observations immediately preceding the change point, provided those observations belong to the stable pre-change regime:

\[
W_{\mathrm{pre}} = \frac{1}{100}\sum_{t=t_0-100}^{t_0-1} w_t.
\]

For a run that meets the recovery criterion, define the recovery-period width mean over the post-change interval from \(t_0\) through \(t_r\), inclusive:

\[
W_{\mathrm{rec}} = \frac{1}{t_r-t_0+1}\sum_{t=t_0}^{t_r} w_t.
\]

Absolute and relative width costs are

\[
\Delta W_{\mathrm{rec}} = W_{\mathrm{rec}} - W_{\mathrm{pre}},
\]

\[
\mathrm{RelativeInflation} =
100 \times \frac{W_{\mathrm{rec}}-W_{\mathrm{pre}}}{W_{\mathrm{pre}}}.
\]

Report relative inflation only when \(W_{\mathrm{pre}}\) is safely above zero; always report the absolute baseline and recovery widths as context.

**Caveats:**
- The recovery-period average includes the immediate post-shift period and ends at the recovery declaration. It is not a causal estimate of the cost attributable only to the algorithm.
- Width cost is undefined as a completed-recovery metric for runs that never recover within the horizon. For those runs, separately report width over a fixed post-change horizon and label it as such.
- Width comparisons are meaningful only when methods use compatible prediction targets, predictors, scores, and information/feedback assumptions.

## 6. Initial synthetic scenarios

### Scenario A — Stationary control
No shift occurs. Measure baseline coverage and width, and check whether the recovery rule generates misleading recovery signals in a stationary setting. Since no actual change occurs, do not treat a recovery time as a post-shift recovery outcome.

### Scenario B — Abrupt response-mechanism (concept) shift
Change the relationship between features and response at a known time while keeping the feature distribution fixed. Specify the exact response-generating equations and at least two prespecified shift magnitudes.

### Scenario C — Abrupt response-noise variance shift
Change response-noise variance at a known time while keeping the feature distribution and conditional mean fixed. Prespecify shift magnitudes.

### Scenario D — Covariate shift (add only after the first three are working)
Change the feature distribution while keeping the response mechanism explicitly defined. Verify that the scenario actually changes the marginal distribution of features and does not inadvertently introduce a different type of shift.

The exact data-generating equations, parameters, change point, burn-in, and evaluation horizon must be recorded in configuration files before the main runs. Do not select shift magnitudes after seeing which ones favor a particular method.

## 7. Methods and comparison fairness

Candidate methods for a compatibility audit:
- Adaptive Conformal Inference (ACI) as an initial baseline.
- DtACI, if its implementation and assumptions are compatible with the selected protocol.
- RetroAdj, subject to reproducing its intended prediction/calibration setup.
- DriftOCP, subject to checking which algorithmic variant and feedback assumptions are being compared.

Before placing any methods on the same leaderboard, document for each:
- predictor and whether it is fixed or updated online;
- nonconformity score and interval construction;
- calibration data and initialization;
- what feedback is available and when;
- update rule and hyperparameters;
- whether the method is designed for the same prediction setting.

Use matched data streams and common evaluation code wherever possible. If methods have materially different predictor or feedback assumptions, report separate comparison tracks rather than claiming a clean head-to-head ranking. Do not infer an algorithm's compatibility merely because its code runs.

## 8. Repetitions, uncertainty, and reporting

- Begin with **5 seeds** for the pilot.
- Plan initially for **at least 30 independent seeds** per main configuration, subject to feasibility and pilot variability.
- Reuse identical generated streams across methods when their assumptions allow this.
- Report per-run values, aggregate means and standard deviations, and confidence intervals for key comparisons.
- Report the fraction of runs not recovered within the horizon.
- Preserve raw outputs and failed runs; document exclusions and their reasons.

Thirty seeds are a starting budget, not a guarantee of sufficient statistical power. Increase repetitions if pilot variability or uncertainty intervals show that comparisons are too imprecise.

## 9. Sensitivity analyses

After the primary settings have been fixed, test:
- coverage windows: 50, 100, and 200 observations;
- alternative tolerance bands, including ±5 and ±10 percentage points around target;
- persistence requirements: 25 and 50 consecutive eligible estimates;
- multiple prespecified shift magnitudes;
- a reasonable alternative pre-change baseline length.

The primary analysis uses the 100-observation window, 85%–95% band, and 50-observation persistence requirement. Sensitivity analyses test how dependent the conclusions are on these choices; they must not be used to cherry-pick the preferred configuration.

## 10. Reproducibility requirements

- Set and log random seeds.
- Store all scenario parameters and method configurations.
- Log per-step prediction intervals, outcomes, coverage indicators, and widths.
- Save raw per-run results before aggregation.
- Separate simulation, metric calculation, and plotting code.
- Record software versions and dependencies.
- Unit-test recovery-time logic and width-cost calculations using hand-constructed examples.
- Make the evaluation horizon and non-recovery handling explicit.
- Preserve failed runs and record any exclusions.

## 11. Planned outputs

1. Configuration files for each scenario and method.
2. Per-step and per-run result files.
3. A table of recovery times and non-recovery rates.
4. A table of pre-change width, recovery-period width, absolute width cost, and relative inflation.
5. Coverage and interval-width trajectories with uncertainty bands.
6. Sensitivity-analysis figures and tables.
7. A limitations section covering metric dependence, synthetic-data assumptions, and method-comparison constraints.

## 12. Decision gates

1. Verify metrics against hand-calculated examples.
2. Complete a one-scenario, one-method end-to-end prototype.
3. Audit implementation and feedback compatibility for each candidate method.
4. Run a five-seed pilot and inspect raw trajectories and failure cases.
5. Confirm the data-generating processes and method settings.
6. Freeze the primary protocol before running the full set of experiments.
7. Discuss the protocol with a potential faculty mentor before treating the research gap or publication prospects as established.

## 13. Remaining caveats

- The recovery rule measures sustained *empirical rolling coverage*; it does not prove conditional or distribution-free coverage has been restored.
- A 100-observation window and 50-step persistence can delay recovery declarations and can make the metric sensitive to the evaluation horizon.
- Concept shift, response-variance shift, and covariate shift must be operationally distinguished in the simulation code.
- Method comparisons can be unfair if predictors, scores, or feedback differ.
- Recovery-period width inflation is descriptive and should be reported alongside coverage and non-recovery, not used as a standalone score.
- Novelty remains provisional until the final related-work check and mentor discussion.
