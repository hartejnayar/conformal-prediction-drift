# RetroADJ

RetroAdj uses a moving window of 250 observations to evaluate local coverage and interval width in its synthetic experiments. It also tests real data, including an AIG stock-price series around the 2008 financial crisis, where coverage drops and interval widths change around the regime shift. 

This is already very close to our planned approach. The paper does not merely mention recovery in passing: its experiments explicitly investigate how quickly coverage adapts and how interval widths behave.

Our proposed rolling window of 100 observations and a requirement to remain within a tolerance band for 50 observations would make our measurement protocol more explicit, but changing those parameters alone would not establish a meaningful research contribution.

# DriftOCP 
DriftOCP already demonstrates that the choice of adaptation method affects how quickly calibration responds to a change, and that coverage and interval width should be considered together.

RetroAdj goes further in explicitly framing recovery time as a measure of adaptation. Consequently, we cannot claim novelty just because our experiment measures recovery time, uses abrupt changes, or plots width and coverage together.

There may still be a worthwhile study here, but the contribution needs to be narrower and evidence-based.