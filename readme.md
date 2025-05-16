# Modeling and Pricing Temperature Derivatives with Historical and AR(1) Residuals

### Disclaimer : This paper / project is still in-progress. Implementation logic as well as the writing is subject to change

## Abstract of the research paper

This project presents a data-driven framework for pricing temperature-linked derivatives, specifically contracts based on Cooling Degree Days (CDDs). Motivated by real-world hedging needs in the energy and insurance sectors, we develop a regime-aware pricing model grounded in conditional expectation theory. The methodology relies on simulating temperature paths using Monte Carlo techniques, employing both i.i.d. and AR(1) residual models, and comparing them against a benchmark derived from historical CDD distributions.

We evaluate each model based on pricing accuracy, probabilistic confidence intervals, and out-of-sample robustness. Surprisingly, our findings suggest that i.i.d. noise models outperform AR(1)-based approaches in certain regimes despite the latter’s theoretical alignment with temperature autocorrelation. The final model not only predicts contract prices within a tight error margin but also provides actionable bounds for trade execution, enhancing real-world decision-making. 



