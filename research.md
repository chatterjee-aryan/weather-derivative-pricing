# Modeling and Pricing Temperature Derivatives with Historical and AR(1) Residuals

##### Aryan Chatterjee 

# Abstract 

This project presents a data-driven framework for pricing temperature-linked derivatives, specifically contracts based on Cooling Degree Days (CDDs). Motivated by real-world hedging needs in the energy and insurance sectors, we develop a regime-aware pricing model grounded in conditional expectation theory. The methodology relies on simulating temperature paths using Monte Carlo techniques, employing both i.i.d. and AR(1) residual models, and comparing them against a benchmark derived from historical CDD distributions.

We evaluate each model based on pricing accuracy, probabilistic confidence intervals, and out-of-sample robustness. Surprisingly, our findings suggest that i.i.d. noise models outperform AR(1)-based approaches in certain regimes despite the latter’s theoretical alignment with temperature autocorrelation. The final model not only predicts contract prices within a tight error margin but also provides actionable bounds for trade execution, enhancing real-world decision-making. 

# Introduction

Weather plays a critical role in the operations and financial performance of numerous industries. For instance, an energy company in Canada may wish to hedge against abnormally warm winters, while agricultural or insurance firms may be vulnerable to seasonal temperature anomalies. To manage this risk, **weather derivatives**—contracts that provide payouts based on weather indices—have become effective tools.

Among these, **temperature derivatives** are the most widely used. These contracts typically pay out based on either **Heating Degree Days (HDD)** or **Cooling Degree Days (CDD)**, which are metrics that measure how far and for how long the daily temperature deviates from a base threshold.

In this research, we approach the pricing problem from the perspective of a **heating energy provider in Chicago**. Our objective is to hedge against reduced revenue during the **summer months**, when demand for heating is naturally low. To do this, we aim to price **CDD derivatives** for the months **May through August**, using a simulation-based approach that incorporates historical structure and regime-aware behavior.

Rather than relying solely on historical data, we propose a **Monte Carlo approach**. This method integrates observed seasonal behavior, regime forecasts (e.g., unusually hot summer), to generate plausible future temperature paths. This leads to more accurate estimation of potential outcomes—and therefore, more robust pricing of weather derivatives.

A typical payoff function for a temperature derivative is as follows :

$$ T_i = \frac{T_{\text{max}} + T_{\text{min}}}{2} $$

Let $T_b$ be the benchmark temperature (typically around 65°F or 18°C). The indices are defined as:

- $$ \text{HDD}_i = \max(T_b - T_i, 0)  — \text{measures the need for heating} $$
- $$ \text{CDD}_i = \max(T_i - T_b, 0)  — \text{measures the need for cooling} $$

The cumulative index over a contract window is:

$$ H = \sum_{i=1}^n \text{HDD}_i \quad \text{or} \quad C = \sum_{i=1}^n \text{CDD}_i $$

A common option-like payoff structure is:

$$ \text{Payoff} = \alpha \cdot \max(H - K, 0) $$

Where:
- $ \alpha $ is the notional amount
- $ K $ is the strike (threshold HDD/CDD)

These contracts take various forms, including **options**, **futures**, and **swaps**, and are often customized to suit specific hedging needs.

# Methodology

The method followed consists of 3 distinct steps as outlined below. 

##### 1. Coming up with a pricing theory

We price temperature derivates from the point of view of someone who is trying to hedge against weather risk. The derivate is priced in such a way that the estimated loss is 0 in the end. The pricing theory relies heavily on the estimation of a probability distribution function ($f_X(x)$) for the CDDs .

##### 2. Estimating the probability distribution function of CDDs

Three different models are proposed to aid in function estimation

- Model 1 : Monte Carlo Simulator with i.i.d noise 
- Model 2 : Monte Carlo Simulator with AR(1) noise
- Model 3 : Historical CDD Sampler (Benchmark model)

##### 3. Model evaluation

Models are evaluated on in-sample as well as out-of-sample data. Evaluations are done on the following 4 factors : 

1. Mean absolute pricing error 
    - By how much was the model off on average . 
    - Being off here is defined as the absolute value of the difference between the estimated price and the actual price which would have led to 0 loss
2. Probability of pricing error being within a certain percentage 
4. Hedging effectiveness
    - It is not necessary that a trade will be possible exactly at the price which the model prescribes
    - Assuming trades are placed at the price which the model prescribes , how much loss is averted and what is the cost benefit of that loss aversion ?
    - In what band of prices around the prescribed price can trades be placed such that there is a reasonable expectation of loss aversion and cost benefit. Basically , what is the maximum price a trader who is using the model should settle for ?

Hedging effectiveness is only checked for the model which performs the best on the first 3 criteria on out-of-sample data

All 3 steps of the methodology are described in detail in the following sections

## Pricing theory

The PnL function for the CDD derivative is as follows :

$$ \text{Gain} = \alpha . max(X - K , 0) - C $$

Where $ C $ is the cost of the derivative.

We think in terms of hedging out our loss. So given a formula for loss and a formula for the payoff , the price has to be set such that net loss = 0 .

We can assume that the heating provider makes a certain loss for every CDD over a certain threshold , else there is no loss . This loss function would also look like a derivative 

Thus, the function which denotes the loss incurred by the energy company would be as follows. Note, that we assume the same strike $ K $ for simplicity

$$ \text{Loss} = \lambda . max(X - K, 0) $$

We need hedge in such a way that the expected loss equals the expected gain

$$ \mathbb{E}[Loss] = \mathbb{E}[Gain] $$

$$ \mathbb{E}[\lambda . (X - K) | X > K] = \mathbb{E}[\alpha . (X - K | X > K)] - C $$

$$ C = \mathbb{E}[\alpha . (X - K ) | X > K] - \mathbb{E}[\lambda . (X - K) | X > K] $$

$$ C = \mathbb{E}[(\alpha - \lambda)(X - K) | X > K] $$

$$  C = (\alpha - \lambda) . (\frac{\int_{K}^{\infty} x f_X(x) \, dx}{\int_{K}^{\infty} f_X(x) \, dx}   - K)$$



$$ \text{We can say that C is parameterized by } X,K,\alpha,\lambda $$

$$ Thus , C = G(X,K,\alpha,\lambda) $$

Estimating the Probability Density Function is the crux of the problem here . Given such a function , simple numerical integration can be done to provide a price estimate. 

### Regime awareness

A regime is defined as a percentile band. Weather forecasters are capable of providing seasonal forecasts regarding the percentile severity of a season. For example, *** the coming summer would be in the top 10 percentile in terms of heat as compared to historically seen summers *** . This is the most important insight which is to be used in pricing of weather derivatives. Because such forecasts are provided much before the season, they provide critical information to players trying to hedge out weather risk . 

This paper however does not bother with trying to go into details of ascertaining whether or not a regime forecast is correct or the degree of its correctness. Model training and evaluation is done assuming that the regime has been correctly predicted. 

However , the probability of the regime forecast being correct , can be calculated using factors such as ocean currents data , historical accuracy of the weather station, etc.

### Historical data

Historical data is also an important tool concerning generation of the density function . Historically seen CDD values give us a broad overview of what to expect the weather to be like in a particular city or area. Although useful , there is a big caveat here . Enough historical data is not available to capture all possibilities of the CDD values. If we are trying to hedge out weather risk in the summer season over the city of Chicago , we will only have a single CDD value and its corresponding daily temperature data for the concerned window per year , going back to N years. Practically speaking, N will never be large enough for the historical data to suffice by itself and stand the pressure of out-of-sample tests.

### Data generation

Historical data is used as a seeding agent for generating distributions which can withstand out-of-sample testing. Firstly , a sine wave is fit to daily temperature data . This wave acts as a baseline for our simulated temperature paths. Next, **Monte Carlo Simulations** are performed to generate CDD values. 

Two different noise methods are used in these simulations are their effectiveness compared while evaluating the models :

1. Method 1 : i.i.d noise

Historical residuals are calculated . A Gaussian KDE distribution is created for these residuals . For each simulated path , a path of i.i.d noise is created by sampling from the residual distribution. 

2. Method 2 : AR(1) noise

Autoregressive noise is more realistic for temperature paths. If temperature is high one day, the chances that it will oscillate wildly in the opposite direction the next day is low. Although AR(1) noise may converge over multiple simulations into i.i.d noise , because of the path dependence of CDD values, we still consider evaluating this method. 

In addition to the 2 models outlined above, a 3rd benchmark model is also made . The benchmark model merely takes in historical CDD values and fits a distribution to them.

Models created :

1. Monte Carlo Simulator with i.i.d noise sampled from a generated historical residuals distribution (Version 1)
2. Monte Carlo Simulator with AR(1) noise generated by fitting values to historically observed temperature paths (Version 2)
3. Historical CDD Sampler (Version 3 - Benchmark)

It is important to note , that all 3 models are regime aware . That is, they create seperate distributions for each regime considered . Naturally, they also take in the regime as input when pricing the temperature security


### Step by step method to generate a probability density function

#### 1. Sine curve fitting

A sine curve is fit to historical daily temperature data for the city of chicago . The sine curve acts as our baseline for residuals calculation and Monte Carlo path simulations for temperature.

#### 2. Regime wise CDD sampling

Historical CDD values are first sampled as per the regime and put into the regime buckets.

#### 3. Creating a historical seed distribution

For each regime bucket , a Gaussian KDE is fit . These distributions act as the sole reference point for the benchmark model.

#### 4. Monte Carlo Simulations

For model versions 1 and 2 , Monte Carlo Simulations must be performed to enhance the seed distribution. The simulation method may either be i.i.d noise based or AR(1) noise based.

1. Model 1 :

    The following steps are run for each regime bucket. 

    - CDD values are fetched
    - Historical temperature paths for the corresponding values are fetched
    - Residuals are calculated for each path . $ \epsilon _i  = T_i - \mu_i $ . Where $\epsilon$ is the residual (noise value) , $T_i$ is the temperature for day i and $\mu_i$ is the sine wave value for day i
    - A Gaussian KDE is fit on the historical residuals found
    - For each simulation :
        - A noise path of length equal to the length of the concerned window is created by sampling values from the residuals distribution
        - Noise values are added to the sine wave over the window 
        - CDD value is calculated for the simulated path 
    - A Gaussian KDE is fit over the CDD values thus simulated

2. Model 2 :

    The following steps are run for each regime bucket

    - CDD values are fetched
    - Historical temperature paths for the corresponding values are fetched
    - Residual path for each temperature path is calculated using the formula $ \epsilon _i  = T_i - \mu_i $
    - An AR(1) model is fit to the residual path
    - A numer of AR(1) noise paths are generated and added to the sine wave to get final temperature paths
    - CDD for each simulated temperature path is calculated
    - A Gaussian KDE is fit over the CDD values thus simulated


## Model Evaluation 

A Gaussian KDE is fit to in-sample or out-of-sample data . N CDD values are sampled as per the regime and the following metrics are calculated . The metrics are calculated per regime. 

### Mean absolute pricing error 

$$ C_{actual} = \text{ actual price which would perfectly offset the loss}\ $$

$$ C_{estimated} = \text{ estimated price by the model} $$

$$ E_i = \frac{| C_{\text{actual i}} - C_{\text{estimated for regime}}| }{C_{\text{actual i}}} $$

$$ E = \frac{1}{N} . \sum_{i}^{N} E_i $$

### Probability that the mispricing falls within a significance value 

  $$ \nu = \text{ value below which the mispricing must be} $$

  $$ S_i = 1 \text{ ; if   } E_i <= \nu  $$

  $$ S_i = 0 \text{ ; else } $$

  $$ S = \frac{1}{N} . \sum_{i}^{N} S_i $$

For the sake of adding clarity to discussion , we choose 2 values for $ \nu $ : 0.1 and 0.2

### Hedging usefulness

Model which performs the best on out-of-sample data in the 2 above metrics , is then chosen for this step. It is important to note that the models are not trying to predict the price exactly . The purpose of the model is to provide an estimate for the price to be paid for a CDD temperature in order to offset weather risk .

#### Protection against loss 


$$ Loss_{unhedged} = \lambda . max(CDD-K,0) $$

$$ Loss_{hedged} = \lambda . max(CDD-K,0) - \alpha . max(CDD-K,0) + C_{paid} $$

$$ Loss_{averted} = \frac{Loss_{unhedged} - Loss_{hedged}}{Loss_{unhedged}}  $$

$$ L = \frac{1}{N} . \sum_{i}^{N} Loss_{\text{averted i}} $$



#### Cost benefit 


$$ CB_i = Loss_{\text{averted i}} / C_{paid} $$

$$ CB = \frac{1}{N} . \sum_{i}^{N} CB_i $$

#### Probability of effective hedge 

$$ P_i = 1 \text{ if } CB_i >= \varphi  $$

$$ P_i = 0 \text{ else } $$

$$ P = \frac{1}{N} . \sum_{i}^{N} P_i $$

The above 3 metrics are calculated under 2 scenarios :

1. Scenario 1 : the derivative can be traded at the exact the price recommended by the model
2. Scenario 2 : the derivate has to be traded at some value $ C + \epsilon $ of the price

In scenario 2 , we are basically trying to check how high can the value of $\epsilon$ be and still have an acceptable $CB$ and $P$ values. In this case, a lower value of $\epsilon$ is better as this represents the fact the model's price estimate is a good estimate to trade the security at. A larger value for $\epsilon$ would mean that the model's price estimate need not be taken seriously and a trader can accept much higher prices from the market.

# Results

1. Mean absolute pricing error
2. Probability of pricing error to be within a specified range

### In-sample test results

| Model | Regime | Mean pricing error | Prob that pricing error < 0.1 | Prob that pricing error < 0.2 |
|-------|--------|--------------------|-------------------------------|-------------------------------|
|   1   | normal |      0.24          |     0.23                      | 0.45                          |
|   1   | warm   |      0.11          |     0.45                      | 0.87                          |
|   1   | warmer |      0.12          |     0.43                      | 0.83                          |
|   2   | normal |      0.26          |     0.22                      | 0.45                          |
|   2   | warm   |      0.11          |     0.46                      | 0.91                          |
|   2   | warmer |      0.12          |     0.44                      | 0.82                          |
|   3   | normal |      0.25          |     0.22                      | 0.44                          |
|   3   | warm   |      0.11          |     0.44                      | 0.90                          |
|   3   | warmer |      0.13          |     0.40                      | 0.78                          |



### Out-of-sample test results

| Model | Regime | Mean pricing error | Prob that pricing error < 0.1 | Prob that pricing error < 0.2 |
|-------|--------|--------------------|-------------------------------|-------------------------------|
|   1   | normal |      0.18          |     0.28                      | 0.54                          |
|   1   | warm   |      0.15          |     0.25                      | 0.71                          |
|   1   | warmer |      0.11          |     0.49                      | 0.93                          |
|   2   | normal |      0.17          |     0.28                      | 0.60                          |
|   2   | warm   |      0.11          |     0.43                      | 0.91                          |
|   2   | warmer |      0.11          |     0.45                      | 0.88                          |
|   3   | normal |      0.20          |     0.26                      | 0.50                          |
|   3   | warm   |      0.13          |     0.38                      | 0.83                          |
|   3   | warmer |      0.13          |     0.40                      | 0.78                          |

## Evaluation of hedging usefulness

Because of the results seen above , a mixed model approach was used for hedging. Model 1 was used only for the warmer regime and Model 2 was used for the normal and warm regimes . The last columns is of primary interest here . Average loss averted is influenced by the values of $\alpha$ and $\lambda$ chosen. However, the final value , the percentage of times that the cost benefit > 1 , is indicative of the payoff received per amount of price paid. 

### Trading at the recommended price 

| Regime | Average Loss averted  | Average Cost benefit | Percent of times that cost benefit > 1|
|--------|---|------------------|---------------- |
| normal | 1.12 | 1.34 | 0.75|
| warm | 1.07 | 1.25 | 0.94
| warmer | 0.99 | 1.01 | 0.42


### Trading at 12 % + recommended price 

| Regime | Average Loss averted  | Average Cost benefit | Percent of times that cost benefit > 1|
|--------|---|------------------|---------------- |
| normal | 1.02 | 1.09 | 0.58|
| warm | 0.99 | 1.01 | 0.51
| warmer | 0.86 | 0.79 | 0.18

# Discussion

The 3 regimes used for the test were as follows : 
- Normal : 25th to 75th percentile of CDD values
- Warm : 50th to 75th percentile of CDD values
- Warmer : 75th to 100th percentile of CDD values 

### Performance of the AR(1) model on the warmer regime 

The AR(1) model did not perform as well as the i.i.d noise model in the warmer regime . As shown by visualizations in the Appendix, the AR(1) model overestimated temperature CDDs in that regime. Reason for this could be that i.i.d noise can fluctuate wildly. Meaning , if temperature of day i is extremely high , it might go way below on day i+1 . However, with AR(1) noise , temperature value on day i+1 would still be relatively high because of the influence of the last day's noise. But this plays out in favour of the AR(1) model in the normal and warm regimes. Because the model has a more realistic path generation process, it's generated distribution is more accurate for less severe regimes.

### Hedge evaluation

Given the performances of the models on out-of-sample data across regimes , a mixture model was selected for hedging evaluation . The AR(1) noise model was used for the normal and warm regimes. Whereas the i.i.d noise model was used in the warmer regime to react to tail events. 

Trading at the recommended price resulted in a average cost benefit of greater than 1 in all 3 regimes. This is important as this shows that the hedge essentially pays for itself. If this were not the case , some other method of hedging should be chosen. Also, trading at just 12 % higher than the recommended level made those values drop really close to one , making trading at 12 % higher price than the model prescribes unfeasible. This is important as this implies following the model's recommendation is useful. Values for average loss averted are greater than 1 while trading at the recommended price. But this is most likely due to the values of $\alpha$ and $\lambda$ chosen. 

### Final thoughts

It seems smart to always use some mix of the i.i.d noise and the AR(1) models. Because the i.i.d noise model allows for fluctuations, it cleanly incorporates extreme behaviours while generating the PDF for CDDs.  Naturally, the mixture would depend on our estimate about the possibility of tail events.  

Another thing to consider is one's estimate of how good a regime forecast is. Being regime aware plays a much bigger role here than the type of noise model we use. Because we took the regime prediction for granted , we effectively integrated over a much smaller region, leading to better estimates. In reality, we might not be as confident about the regime prediction. This model should be used along with another one which performs regime prediction  or comments on the accuracy of a weather station's forecast.


# Appendix

## Visualizations


### CDD distributions created by the i.i.d noise Monte Carlo simulator for each regime

![Fig 1.1](./images/normal_regime_iid.png)

![Fig 1.2](./images/warm_regime_iid.png)

![Fig 1.3](./images/warmer_regime_iid.png)

### CDD distributions created by the AR(1) noise Monte Carlo simulator for each regime

![Fig 2.1](./images/normal_regime_ar.png)

![Fig 2.2](./images/warm_regime_ar.png)

![Fig 2.3](./images/warmer_regime_ar.png)

