# Rainbow-Option-Pricing
Monte Carlo methods to price a type of Rainbow Option

Pricing a Rainbow Option using Monte Carlo Methods

Procedure:

The first step in order to price the option was to gather historical data for all the three indices along with the risk free rates (3 month t-bill rate) for each day. The dataset is from 2000-11-06 till 2024-10-03 (YYYY-MM-DD). The values for the price of the underlying assets are actual historical values, the values for the interest rates could not be obtained for the duration of the analysis. As a result, these have been assumed to be in the range of 6% for India, and 3-4% for the US and the UK. These values can be customized based on availability of actual 3 month t-bill rates in the three countries.

The next step is to use sobol sampling and antithetic variates to generate random variables, this helps in reducing variance, then convert these to standard normal random variables and then match the first and the second moments to ensure that the generated random variables have mean 0 and variance 1. 

Then I have taken the correlation between the returns of the three assets across a 5 year period in order to find the correlation between the three assets. The choice to take 5 years has been motivated after consultation with industry practitioners and academic research papers. In order to correlate the random numbers I have taken the cholesky decomposition of the correlation matrix.

Now I have used geometric brownian motion in order to simulate stock prices with the aforementioned random variables. For the mean returns, since I am valuing the option under the risk neutral measure I have chosen the risk free rate. For the volatility I have chosen 3 year historical volatility of returns for each of the three stocks.

Now the iterative process starts, I iterate over each quarter to find the simulated asset prices at the end of the quarter using monte carlo simulations 10,000 times. I then calculate the new portfolio value based on the asset that performed the best and second best. 

Now this process is repeated for 11 more quarters, i.e. a total of 3 years. Now the payoff is calculated for the last time. This is the final value of the portfolio. Under the risk neutral measure the value of the option will be the discounted value of this at the risk free rate. However, since the risk free rates differ for all the three countries, I have found out the contribution of each asset for each period and discounted the contribution of that asset to the final portfolio value with that country's risk free rate. For example, if nifty’s contribution was 50% and ftse and gold’s was 33% and 17%. Then 50% of the final value is discounted at India’s risk free rate, 33% at UK’s and 17% at US. This is done to keep the valuation in line with the risk neutral measure assumption that I have taken.

I repeat this process again by calculating new values for the volatility and the correlation, taking the same historical time periods; 3 years and 5 years respectively and performing the analysis for the next 3 years and so on.

Finally, in order to calculate the delta neutral hedges on a daily basis, I minimize the variance of the value : (discounted payoff on day i) + h1*price_asset1 + h2*price_asset2 + h3*price_asset3. I use scipy.optimise.minimise to do this. I generate 10,000 sample simulations, so the discounted payoff on day i has those 10,000 values, similarly 10,000 values for the 3 asset prices. The only unknown variables that are left to solve for are the three hedge ratio’s h1,h2 and h3.

To calculate the cash position, I use the change in the delta hedge values to find the allocation to cash on a daily basis

Improvements that I am working on:

Using copulas to simulate the three correlated stock prices.
Using other volatility models like garch(1,1) and/or stochastic volatility models to forecast daily volatilities of the stocks for a three year period. These might offer an upgrade to using the 3 historical values.











