# Paper Notes: Conformal Inference for Online Prediction with Arbitrary Distribution Shifts

## Citation and link
- **Authors:** Isaac Gibbs; Emmanuel Candès
- **Year:** 2024 (PDF dated October 9, 2023)
- **Venue:** Not stated in the supplied PDF (manuscript/preprint). Verify official venue.
- **DOI / official URL:** Not provided in the supplied file. Verify in Zotero/publisher record.
- **Link in project:** Local PDF; add official record to Zotero collection `01 - Foundational Papers`.

---

## 1. Problem
How to construct prediction sets online when the distribution of sequentially arriving data \((X_t, Y_t)\) changes arbitrarily over time - without assuming exchangeability and without knowing the rate or type of shift.

Formally: at each \(t\), using past data \(\{(X_s,Y_s)\}_{s<t}\) and new \(X_t\), output \(\hat{C}_t\) targeting \(\mathbb{P}(Y_t \in \hat{C}_t) \approx 1-\alpha\), where the data-generating mechanism may drift.

---

## 2. Why it matters
Standard conformal inference guarantees coverage under exchangeability/i.i.d. data (Theorem 2.1). Under distribution shift these assumptions fail.

**What fails with existing approaches:**
- **ACI (Gibbs & Candès, 2021):** adapts online but requires step size \(\gamma\) proportional to the unknown rate of change in \(\alpha_t^*\); also relies on assumptions that the score function and reference distribution \(\mathcal{D}\) are fixed over time — unrealistic under drift.
- **AgACI (Zaffran et al., 2022):** learns \(\gamma\) but weights all historical losses cumulatively, so it is slow to react to abrupt shifts.
- **MVP (Bastani et al., 2022):** targets long-term/threshold-calibrated coverage but shows little local adaptivity; often behaves like a constant baseline.
- **Naive constant \(\alpha_t=\alpha\):** ignores shift entirely; local coverage swings or is biased.

---

## 3. Main contribution
**DtACI (Dynamically-Tuned Adaptive Conformal Inference):** modifies ACI by adding an online step-size tuning step. Multiple ACI "experts" run in parallel with candidate \(\gamma\) values; an exponential reweighting scheme (adapted from Gradu et al., 2020) chooses/averages among them based on historical pinball loss.

**New relative to prior work:**
- Adaptive to both the **size and type** of distribution shift, without prior knowledge of the shift rate.
- **Local dynamic-regret bounds** over any interval \(I=[r,s]\), controlling deviation from the local optimal \(\alpha_t^*\). The authors state no similar local results exist for AgACI or MVP.
- No assumptions that the conformity score \(S(\cdot)\) or score distribution \(\mathcal{D}_t^y\) are fixed over time — both may be recalibrated as data arrive.
- Theory connects pinball loss to \((\alpha_t-\alpha_t^*)^2\) and to local coverage (Prop. 3.1, eq. 6).
- Long-term coverage bound (Thm. 3.2) when \(\eta_t,\sigma_t\to0\).

---

## 4. Method
**Core idea:** ACI update \(\alpha_{t+1}=\alpha_t+\gamma(\alpha-\mathrm{err}_t)\) is gradient descent on the pinball loss \(\ell(\beta_t,\theta)\). Since \(\gamma\) is unknown, learn it online by expert aggregation.

**Algorithm 1 (randomized) / Algorithm 2 (deterministic weighted average \(\bar\alpha_t=\sum_i p_t^i\alpha_t^i\)):**
- Inputs: observed \(\beta_t\) (smallest \(\beta\) such that \(Y_t\in\hat{C}_t(\beta)\)), candidate \(\gamma\) grid \(\{\gamma_i\}_{i=1}^k\), initial \(\{\alpha_1^i\}\), parameters \(\eta,\sigma\).
- Weights: \(w_t^i\); output \(\bar\alpha_t=\sum_i p_t^i\alpha_t^i\) with \(p_t^i=w_t^i/\sum_j w_t^j\).
- Weight update: \(\bar w_t^i = w_t^i \exp(-\eta\,\ell(\beta_t,\alpha_t^i))\), then \(w_{t+1}^i=(1-\sigma)\bar w_t^i+\bar W_t\sigma/k\) (mixing).
- Each expert updates \(\alpha_{t+1}^i=\alpha_t^i+\gamma_i(\alpha-\mathrm{err}_t^i)\).
- Crucially, exponential reweighting **downweights old data**, unlike AgACI's cumulative loss.

**Hyperparameters:** Candidate \(\gamma\) grid (experiments: \(\{0.001,0.002,0.004,0.008,0.016,0.032,0.064,0.128\}\)); \(\eta,\sigma\) heuristic (e.g., \(|I|=500\), \(\eta=\sqrt{\frac{3}{500}}\sqrt{\frac{\log(500k)+2}{(1-\alpha)^2\alpha^2}}\); variable \(\eta_t\) option in Appendix).

**Assumptions:**
- Mild continuity/monotonicity of \(\beta\mapsto\mathbb{P}(Y_t\in\hat{C}_t(\beta))\) for existence of \(\alpha_t^*\).
- Local coverage bound (eq. 6) needs a density lower bound \(p>0\) on \([0,1]\); coverage interpretation also uses \(L\)-Lipschitz assumption.
- Theorem 3.1 conditions: ordered \(\gamma\) grid with \(\gamma_{i+1}/\gamma_i\le2\), \(\gamma_k\ge\sqrt{1+1/|I|}\), \(\sigma\le1/2\).
- Theorem 3.2: requires \(\eta_t,\sigma_t\to0\) for exact long-term coverage.

---

## 5. Evaluation
- **Datasets:**
  - Simulated: \(Y_t\sim\mathcal{N}(\mu_t,1)\) with (A) stationary \(\mu_t=0\), (B) smooth drift \(\mu_{t+1}=\mu_t+\frac12(\mu_t-\mu_{t-1})+\frac12\epsilon_t\), (C) jump shifts (three regimes; small oscillations then large \(\pm1.5\) then small again).
  - Real: stock-market volatility (GARCH(1,1), most recent 1250 days) for Nvidia, AMD, BlackBerry, Fannie Mae; normalized and unnormalized conformity scores.
  - Real: COVID-19 county-level 7-day-ahead case counts (DELPHI/Facebook survey), least-squares regression on 3 lagged case and survey terms; 4 counties (San Francisco, New York, Miami-Dade, Dallas).

- **Baselines:** AgACI; MVP (modified implementation, \(m=40\), \(\eta=\sqrt{\log m/(4.2m)}\), \(r=800000\)); constant \(\alpha_t=\alpha\) baseline; in simulations, oracle \(\alpha_t^*\) trajectory.

- **Metrics:**
  - Simulated: instantaneous coverage gap \(\mathrm{CG}_x=|\mathbb{P}(Y_t\in\hat{C}(\alpha_t^x)|\alpha_t^x)-(1-\alpha)|\); differences \(\mathrm{CG}_A-\mathrm{CG}_D\), \(\mathrm{CG}_M-\mathrm{CG}_D\); mean step-size \(\bar\gamma_t\).
  - Real: local average coverage over moving window (eq. 7, 500-day for stocks; 200-step for COVID); global coverage frequency; Q–Q plots of local coverage gaps vs. i.i.d. Bernoulli(\(\alpha\)); empirical conditional coverage (eq. 8) with block-bootstrap error bars (100 resamples, block 100).

- **Experimental settings:** 100 simulated trials per setting; \(\gamma\) grid as above; \(\eta,\sigma\) fixed heuristic and variable-\(\eta\) appendix results.

- **Main findings:**
  - **Stationary:** MVP and AgACI slightly outperform DtACI (DtACI cannot set step size exactly 0).
  - **Smooth drift:** AgACI ≈ DtACI; MVP poor.
  - **Jump shifts:** DtACI tracks shift-size changes; AgACI lags when magnitude jumps and fails to shrink again in third regime; MVP poor.
  - **Stocks:** DtACI local coverage near 0.9 across both scores; AgACI nearly identical except small lag at Fannie Mae 2009 crisis; MVP ≈ constant baseline. Q–Q plots align with Bernoulli ideal; conditional coverage mostly covers 0.9.
  - **COVID:** DtACI and AgACI correct for multiple waves; MVP inconsistent; constant baseline biased/swinging.
  - Variable \(\eta_t\) gives nearly identical results to fixed heuristic \(\eta\).

---

## 6. Limitations
**Authors explicitly acknowledge:**
- DtACI adds two unknown parameters \(\eta,\sigma\) (though they argue these are less critical than \(\gamma\) and give principled choices).
- Exact long-term coverage requires \(\eta_t,\sigma_t\to0\), which they do **not** recommend under nonstationary shifts.
- With constant/slowly varying \(\eta,\sigma\), empirical long-term coverage can be biased (they claim small in practice).
- Local coverage bound (eq. 6) requires density lower bound \(p>0\) and, for direct coverage reading, Lipschitz continuity.

**Additional limitations justifiable from evidence:**
- Real-data experiments use simple/low-dimensional models (GARCH, least-squares); no high-dimensional black-box predictor evaluation.
- Prediction-set **size/efficiency** and computational cost are not primary evaluation metrics — only coverage.
- Candidate \(\gamma\) grid range may bound achievable adaptivity; sensitivity not systematically studied.
- Conditional-coverage error bars are not guaranteed under distribution shift (authors say visual aid only).
- Theorem 3.2 long-term result is under decaying parameters, a regime authors discourage in practice; practical constant-parameter version lacks exact long-term guarantee.
- Only four stocks and four counties shown; selection of these is not fully justified beyond data availability.

---

## 7. Relevance to my project
Directly relevant as a **baseline and conceptual reference for online conformal prediction under arbitrary distribution shift**.
- Provides the canonical ACI → DtACI line of work and positions AgACI/MVP as alternatives.
- Supplies metrics I can reuse: local coverage over moving windows, dynamic regret, coverage gap, conditional coverage, step-size trajectories.
- Its local-regret framing is useful if my provisional question concerns **adaptivity speed** or **local (not just marginal) coverage** under abrupt shifts.
- Clarifies the trade-off: stationarity precision vs. nonstationary adaptivity — a useful axis for comparing methods.
- The "reactive vs. learning \(\alpha_t^*\)" critique (Sec. 4.2.2) suggests an evaluation I may want to replicate.

---

## 8. Questions
- How should the target interval length \(|I|\) be chosen, and how sensitive are results to it?
- How sensitive is DtACI to the candidate \(\gamma\) grid (range, density, spacing ratio ≤ 2)?
- Can local coverage guarantees be obtained without the density lower bound / Lipschitz assumptions?
- What is the computational overhead of \(k\) parallel ACI experts vs. ACI/AgACI/MVP?
- How does DtACI behave with high-dimensional covariates and neural-network predictors?
- How large is the long-term coverage bias with constant \(\eta,\sigma\), and how does it vary across domains?
- Why does DtACI not evaluate prediction-set width/efficiency, and would conclusions change if efficiency were included?
- In the COVID experiment, how are counties with missing Facebook data handled, and could selection bias affect conclusions?

---

## 9. My assessment
**What I learned:**
- ACI's step size is the central practical weakness; framing ACI as pinball-loss gradient descent enables standard online-learning tools.
- Exponential reweighting with mixing (\(\sigma\)) gives recency-sensitive expert selection, unlike AgACI's cumulative loss.
- Local dynamic regret is the right theoretical object for nonstationary coverage; it yields coverage bounds scaling with the total variation \(\sum|\alpha_t^*-\alpha_{t-1}^*|\).
- Empirically, adaptivity gains are clearest under **abrupt magnitude changes**, not smooth drift — a nuanced takeaway.

**What remains unresolved:**
- Whether the local coverage guarantee translates to practical reliability under realistic, high-dimensional predictors.
- The magnitude and practical relevance of long-term bias with constant \(\eta,\sigma\).
- Efficiency (set size) and computational cost trade-offs relative to baselines.
- Robustness to hyperparameter/grid choices beyond the reported settings.

**Possible follow-up to investigate (not yet a claimed research gap):**
- Compare DtACI, AgACI, MVP, and ACI on a common benchmark that reports **coverage, set size, and runtime** jointly, including a high-dimensional predictor.
- Test sensitivity to candidate \(\gamma\) grids and to \(\eta,\sigma\) choices to characterize robustness.
- Examine whether the density-lower-bound condition is plausible for real conformity-score distributions (e.g., GARCH normalized scores), and whether weaker conditions suffice.
- Replicate the conditional-coverage diagnostic on additional datasets to test the "not merely reactive" claim.

---

## Reading status
- Abstract: done
- Introduction: done
- Method: done (Algorithms 1–2, Sec. 3 theory)
- Experiments: done (Sec. 4.1–4.3, Appendix figures)
- Full paper: done provided (proofs in App. A.3–A.5 noted, not fully re-derived)
- Pass 3 (critical evaluation of proofs, official metadata/DOI verification): pending