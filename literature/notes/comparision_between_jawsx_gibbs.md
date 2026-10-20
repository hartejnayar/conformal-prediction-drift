Cross-Paper Comparison — DtACI vs JAWS-X
Purpose
This is a literature-synthesis note for Project Apex. It compares two related but not directly interchangeable conformal-prediction papers. The comparison is based on the published papers, not on a claim that the methods have already been tested in one shared benchmark.
1. At a glance
Dimension	Gibbs & Candès — DtACI (2024)	Prinster, Saria & Liu — JAWS-X (2023)
Main setting	Online prediction where the data distribution may vary over time	Standard covariate shift and feedback covariate shift
Main problem	Adapt quickly enough when the shift’s size/type is not known in advance	Construct informative intervals under shift without excessive computation or wasteful sample splitting
Core method	Dynamically combines ACI experts with different step sizes	Weighted jackknife+-based methods: JAW-FCS, JAW-KLOO and WCV+
Main theoretical lens	Local dynamic regret and related local/long-term coverage results under stated conditions	Finite-sample marginal coverage guarantees under stated shift/weight assumptions
Efficiency emphasis	Adaptation and coverage over time; interval width/runtime are not the main focus	Computational cost, use of labeled data, coverage, interval width and model performance
Key empirical finding	Especially useful in some abrupt-shift scenarios; not uniformly best in every setting	WCV+ often gives a useful computational compromise; JAW-KLOO can suffer wider intervals or greater coverage variability at small budgets
Main caution	Theoretical results have conditions; practical parameter choices matter	Correct/appropriate shift weights matter; WCV+ has a weaker bound; results are specific to SCS/FCS


2. Shared foundation
Both papers use conformal prediction to quantify uncertainty around predictions, and both address situations where ordinary exchangeability-based guarantees may not apply directly. Both evaluate coverage, but they focus on different failure modes and different types of shift.
3. The most important difference
DtACI is mainly about adaptation over time. It asks how an online method can adjust its calibration behaviour as the distribution evolves.

JAWS-X is mainly about weighted predictive inference under covariate shift, with a strong efficiency focus. It asks how to retain coverage guarantees in standard/feedback covariate shift while reducing the cost of full conformal methods and avoiding the data-use penalty of split conformal.

Because the settings differ, a direct performance ranking between the two papers would be unjustified without carefully defining a common data-generating setup, valid baselines and comparable guarantees.
4. What this means for the proposed research idea
The provisional idea—comparing reliability and efficiency under covariate and concept drift—is still too broad to count as a demonstrated research gap.
- JAWS-X already studies coverage and interval width alongside computational/statistical efficiency under SCS/FCS.
- DtACI already studies online adaptation under arbitrary time-varying distributions, including abrupt shifts.
- Neither paper alone proves that a joint benchmark across every shift type is novel. A literature review must check other and newer methods, and a cross-setting benchmark would need to address the methods’ differing assumptions fairly.
5. Candidate directions to investigate (not established gaps)
1. Parameter sensitivity: under a carefully defined online-shift benchmark, how sensitive are selected online methods to tuning parameters, and what happens to coverage and interval width?
2. Runtime under matched conditions: compare computational overhead only among methods whose assumptions fit the same data-generating scenario; report wall-clock runtime and model-fit counts separately.
3. Coverage recovery after a shift: define a clear shift schedule and compare how quickly methods recover target coverage, while also measuring interval width.
These are hypotheses for literature review, not claims of novelty. Before selecting one, search for papers that have already studied it and ask a research mentor to help assess feasibility.
6. Immediate next step
Do not start a large experiment yet. First, read the relevant related-work sections and citations in both papers, add the most relevant follow-up papers to Zotero, and update the literature matrix with the precise setting, theorem guarantee, efficiency measures, datasets and limitations for each paper.
