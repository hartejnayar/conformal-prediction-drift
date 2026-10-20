# Paper Notes 2 — JAWS-X: Addressing Efficiency Bottlenecks of Conformal Prediction Under Standard and Feedback Covariate Shift
Citation
- Authors: Drew Prinster, Suchi Saria and Anqi Liu
- Year / venue: 2023, Proceedings of the 40th International Conference on Machine Learning, Proceedings of Machine Learning Research, vol. 202, pp. 28167–28190.
- Official article: https://proceedings.mlr.press/v202/prinster23a.html
- Official PDF: https://proceedings.mlr.press/v202/prinster23a/prinster23a.pdf
- Code: https://github.com/drewprinster/jaws-x
- Reading status: The full published PDF has been consulted for this corrected version. The proof appendices have not been independently re-derived.
1. Problem
The paper studies how to construct informative prediction intervals for black-box machine-learning predictors when the usual exchangeability assumption fails because of standard covariate shift (SCS) or feedback covariate shift (FCS).
- Standard covariate shift: the distribution of inputs X changes between training and test data, while the conditional relationship P(Y | X) is assumed to remain the same; the test distribution is independent of the training sample.
- Feedback covariate shift: the test-input distribution may depend on the training data. This can happen when model predictions influence which examples are selected next, such as in protein design or active learning. The paper still assumes the relevant conditional outcome relationship remains invariant.
This is not the same setting as arbitrary temporal drift in an online stream. The distinction matters when comparing this paper with DtACI.
2. Why it matters
The authors identify two practical challenges in uncertainty quantification under shift:
1. Computational efficiency: full conformal approaches can require many model fits, which can be prohibitive for expensive black-box predictors.
2. Statistical/data efficiency: split conformal reserves data for calibration rather than model training, which can weaken the predictor and lead to wider, less informative intervals.
A useful method should maintain valid coverage while avoiding excessive computation and making effective use of labeled data. Smaller intervals are useful only when the desired coverage is maintained.
3. Main contribution: JAWS-X
JAWS-X is a family of jackknife+-based methods for predictive inference under standard and feedback covariate shift.
A. JAW-FCS
JAW-FCS extends the weighted jackknife+ approach to feedback covariate shift. It uses likelihood-ratio-style weights that account for the shift and its dependence on the training data.

Under the paper’s stated feedback-shift model and an absolute-continuity condition on the shifted input distribution relative to the training input distribution, its theorem gives the marginal coverage bound:

P(Y_(n+1) ∈ C_(n,α)(X_(n+1))) ≥ 1 − 2α.

This is a finite-sample guarantee, but note that the lower bound is 1 − 2α, not 1 − α. Do not describe it as exact target coverage or silently replace it with a 1 − α guarantee.
B. JAW-KLOO
This method uses only K leave-one-out models rather than fitting all n leave-one-out models. It preserves the same stated 1 − 2α finite-sample lower bound under the paper’s conditions.

The choice and sampling of the K points matter empirically. In the experiments, small K can lead to overly wide/uninformative intervals for deterministic selection; random selection can increase coverage variability.
C. WCV+
Weighted cross-validation+ uses K fold-based models instead of all leave-one-out models. It also applies to SCS and FCS.

Its FCS guarantee is weaker than the JAW-FCS/JAW-KLOO bound by an additional nonnegative term involving normalized weights for points in the same fold. Thus, while it retains a finite-sample guarantee, it is not correct to say it has exactly the same guarantee as JAW-FCS in every setting.
4. Assumptions and scope
- The shift is covariate shift: the input distribution changes while the conditional outcome relationship P(Y | X) is treated as invariant.
- For FCS, the test-input distribution can depend on the training data, which creates train–test dependence.
- The stated FCS theorems assume the shifted input distribution is absolutely continuous with respect to the training input distribution for all relevant training-data realizations.
- The guarantee is marginal coverage, not conditional coverage for every individual X.
- The paper’s guarantees assume the appropriate likelihood-ratio weights are available/valid. The authors explicitly identify weight estimation error as a limitation for the guarantees, even though their active-learning experiment also tests estimated weights empirically.
5. Evaluation
Experiments and datasets
1. Protein design under FCS: two fluorescence targets (“red” and “blue”) from combinatorially complete protein datasets. The test/design distribution is shifted toward sequences with higher predicted fitness. The experiments use neural-network and random-forest predictors, several shift magnitudes, 20 repetitions, 192 training examples and 200 test points per repetition.
2. Active learning under FCS: an airfoil dataset, where the feedback-shift weights need to be estimated. The study uses 30 experimental repetitions, eight query iterations and 16 queried points per iteration.
3. Standard covariate shift: four UCI datasets—airfoil self-noise, red wine quality, superconductivity, and communities and crime—to compare computational relaxations with SCS baselines.
Baselines and metrics
Baselines include jackknife+, standard split conformal, weighted split conformal, full conformal under FCS, standard CV+, and JAW-SCS where applicable.

Metrics include:
- empirical marginal coverage against a target level (often 0.9 in the experiments);
- median prediction-interval width (smaller is more informative if coverage is maintained);
- coverage variance across repetitions/settings;
- model performance, including predicted fitness in protein design and validation mean-squared error in active learning;
- computational budget measured by the number of fitted predictors K and comparison with the much more expensive full conformal FCS procedure.
Main findings reported by the paper
- In the protein-design experiments, JAW-FCS and weighted split conformal maintained target coverage across tested shift magnitudes, while unweighted exchangeability-based baselines lost coverage. JAW-FCS generally produced narrower intervals and higher predicted fitness than weighted split conformal, in part because it avoids sample splitting.
- In the tested computational-budget settings, WCV+ maintained target coverage and usually had interval widths comparable to full JAW-FCS. JAW-KLOO could have overly wide intervals at small K; random selection could yield higher coverage variance.
- In the active-learning experiment, the proposed methods empirically achieved target coverage and more informative intervals/lower validation error even with estimated weights. This empirical result does not remove the theoretical limitation that the formal guarantees rely on appropriate weights.
- In the SCS experiments, WCV+ and JAW-KLOO generally maintained the target coverage in the tested settings; WCV+ had interval widths comparable to JAW-SCS, while JAW-KLOO’s behaviour depended on its selection strategy and K.
These are empirical results for the paper’s experiments, not universal performance guarantees beyond the stated theory.
6. Limitations
Explicitly identified by the authors
- The formal guarantees assume the correct/appropriate shift weights. The active-learning results with estimated weights are encouraging empirically, but weight-estimation error and its effect on guarantees remain open.
- The authors identify stronger guarantees for WCV+, better selection of leave-one-out models for JAW-KLOO, and extensions to other shift settings as future directions.
Additional observations from the paper
- WCV+ has a weaker theoretical FCS bound than JAW-FCS/JAW-KLOO, although its empirical results are strong in the reported settings.
- JAW-KLOO’s computational savings can come with wider intervals or increased coverage variability, depending on K and how points are selected.
- The work focuses on SCS/FCS and does not solve arbitrary concept drift or unrestricted temporal distribution change.
- The reported real-world tasks are domain-specific. Their results do not by themselves establish performance in all deployment domains.
- The paper emphasizes coverage, interval width and model performance; it reports computational cost primarily through the number of trained predictors, rather than a universal wall-clock/runtime benchmark across hardware.
7. Relevance to my project
JAWS-X is directly relevant to the efficiency side of my proposed research direction. It shows that efficiency is not one thing: it includes computational cost and statistical/data efficiency, and the resulting interval width must be considered alongside coverage.

It also demonstrates why the shift setting must be specified carefully. JAWS-X handles SCS/FCS; DtACI addresses online prediction under broad time-varying distributions. They are complementary research lines, not automatically interchangeable baselines for a single head-to-head leaderboard.
8. Questions for further reading
- How do the likelihood-ratio weights arise in each setting, and how would one estimate them in practice?
- How does the additional WCV+ coverage-bound term behave as the number of folds and data size change?
- How sensitive are JAW-KLOO results to K and the strategy for selecting the leave-one-out points?
- What is the wall-clock runtime/memory cost under comparable hardware and predictor architectures?
- Which newer methods address weight-estimation error or generalize beyond covariate shift?
9. Assessment
Main takeaway: JAWS-X adapts jackknife+-style inference to covariate and feedback covariate shift, aiming to maintain finite-sample marginal coverage while improving the balance between computation and use of labeled data.

Important precision: JAW-FCS and JAW-KLOO have a stated 1 − 2α lower bound under the theorem conditions. WCV+ has a weaker bound with an additional term. Empirical target coverage at 1 − α in the experiments is a separate observation and should not be confused with the theorem’s worst-case lower bound.

Research-gap status: The paper already explicitly studies computational efficiency, interval width and coverage under covariate/feedback shift. Therefore, “compare efficiency and reliability” alone is not a sufficient novelty claim. A possible new question would need to be narrower and carefully separated from existing work.
