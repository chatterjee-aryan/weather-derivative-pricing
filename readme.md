# Pricing Temperature Derivatives using Monte Carlo Techniques

## Overview of CME temperature derivatives

Temperature derivatives traded on CME primarily rely on 2 indices : HDD and CDD which are defined as follows

$$ 
HDD = \sum_{t} max(\theta_{base} - \theta_t , 0)
$$

$$
CDD = \sum_{t} max(\theta_t - \theta_{base} , 0)
$$

Where $ \theta $ represents temperature. 

HDD stands for Heating Degree Days signifying need for heating. While CDD stands for Cooling Degree Days signifying need for cooling.

These 2 values are then used as underlying for the derivatives which can be options, forwards,etc .

A typical payoff for a HDD forward as follows : 

$$
Payoff = \alpha * HDD
$$

Where $ \alpha $ is called the notional. In the case of CME contracts this is $20.

The purpose of these contracts is to hedge against weather risk.

The purpose of this project is to price options on such forwards.


## Overview of Methodology

In one line : I generate a distribution for HDD by simulating temperature paths . Then, I shift the distribution to match its mean to a selected forward price observed on the market. Finally, use the shifted distribution to price the derivate using the Bachelier model. 

Steps :

1. First, fit a sine wave to the temperature data to capture seasonality
2. Find differences of actual temperature from the sine wave
3. Fit a time series model to the differences to generate temperature paths
4. Calculate HDD for each path to generate the HDD distribution
5. Shift such that expected HDD equals the price of a forward 
6. Use the shifted distribution to price an option under the standard Bachelier model and also compute the option greeks 


