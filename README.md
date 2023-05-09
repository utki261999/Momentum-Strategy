# Momentum-Strategy
A quantitative strategy which trades momentum factor, while being market neutral

At the beginning of each month t, stocks are sorted into 10 deciles based on their cumulative
returns from month t−12 to t−2 (ranking period). We use one month gap between ranking period
and holding period to avoid short term one-month reversals. Time t is the formation date at
which the prediction of momentum factor for t+1 will be calculated.
The momentum strategy goes long a value-weighted portfolio of stocks in the top decile and
sells short a value weighted portfolio of stocks in the bottom decile. The weights of these long
and short portfolios are previously calculated by bootstrapping the portfolios with the objective to
maximize the Sharpe Ratio.
Now to make our momentum strategy better, we add different factors to the model along with
the basic momentum factor.
We use seven variables for predicting momentum return and arrange them in two sets. The first
group contains variables that are obtained from overall stock market and the second group is
related to momentum return time series.
We use market past return (Mkt), change in the market return (MktChg), volatility of the market
(MktVol), cross sectional dispersion of stock returns (Disp) and market illiquidity (Illiq) from the
market in the first group. We also use momentum return volatility (MomVol) and change in
momentum volatility (MVolChg) that come from momentum time series.
We know from the study that, Market volatility on dates of almost all of the momentum crashes
is more than twice of its mean, also market return rebound in most of the momentum crash
events. When market falls, losers fall more than winners (as it’s defined) and then when market
rebounds, stocks that have experienced higher drops will go up more than the others and thus
loser portfolio’s return will be higher than winner.
We have five variables in market group which are market lagged return, market return volatility,
cross sectional stock returns dispersion and illiquidity. We can also use change in market lag as
another predictor. These variables are used to capture market rebound and change in market
situation.
Change in market return (MktChg) is calculated from t − 1 to t . All of the predictors including
market return should be known at time t that is formation date to predict momentum at t + 1.
Market dispersion is cross sectional volatility in the market and contain information about
difference between stocks return. In panic periods dispersion of stock returns is higher and it
can be used to predict momentum return.
For market volatility (MktVol), we can use weekly return of CRSP value weighted return and
calculate standard deviation of past 52 weeks (one year), then get the average of weekly
volatilities in each month to have monthly time series of market volatility. We can use this cross
sectional volatility of stocks returns as a measure of market dispersion (Disp) which captures
dispersion of return cross the market. We can use daily stock returns and calculate variance of
daily return cross all CRSP stocks, then get the average of daily cross sectional variance in
each month and call it market dispersion.
In order to obtain market illiquidity measure (Illiq), similar to Avramov, Cheng and Hameed
(2013), we can use the same methodology. For each stock in each day, absolute daily return is
divided by closing price times the number of traded stocks in that day, then the monthly average
is taken for each stock. Definition of illiquidity can be presented as follows:
where n is the number of trading days in each month, Ri,d is return of stock i on day d, Pi,d is
closing price and Ni,d is number of shares traded during day d.
Market illiquidity (Illiq) is defined as the value weighted average of each stock monthly illiquidity
measure.
For each month, we calculate standard deviation of past six-month momentum return as a proxy
for momentum volatility (MomVol). Similarly, we calculate change in momentum volatility
(MVolChg).
Momentum Return Forecasts: We decide to create a reinforcement learning model which has
these factors as the environment, the reward being excess returns than the market, each time
series cross sectional data represents the state, the action being trading of the stocks. This is
done in order to develop a trading strategy based on the above factors for most optimal returns
as discussed in Yawei Li, Peipei Liu, Ze Wang, "Stock Trading Strategies Based on Deep
Reinforcement Learning".
The optimal action set will contain all the signals of buy/sell using the most accurate weights of
the inputs in the neural net. The objective function will be to maximize the in-sample Sharpe
Ratio obtained from the actions. We also add a recurrent-RNN module to the architecture to
forecast the time series and evaluate the performance of the actions classified by the
reinforcement -learning model.
We use this model to predict the action state of buy/sell, and forecast the corresponding
portfolio returns for the next 42 trading days as it will be used to dynamically hedge the final
portfolio. Thus, the momentum returns, and volatility can be calculated from these forecasts.
These forecasts are then used to dynamically allocate the weights in the long and short
portfolios.
Dynamic Portfolio Weight Allocation: This method is based on the fact that momentum is a
zero-investment strategy, i.e., goes one dollar long and one dollar short at the same time and it
can be weighted in each rebalancing period to construct dynamic risk managed momentum
strategy.
We will also dynamically weight the assets in our portfolio using equation (4) from the
Momentum Crashes paper by Daniel and Moskowitz that was given to us. In our situation, the
forecasted return and volatility calculated by the factors above via Reinforcement Learning. As
stated in Daniel and Moskowitz: “for the objective function of maximizing the in-sample
unconditional Sharpe ratio, the optimal weight on the risky asset (WML) at time t − 1 is
where µ_{t-1} is the conditional expected return on the (zero-investment) WML portfolio over the
coming month, σ^2_{t-1} is the conditional variance of the WML portfolio return over the coming
month, and λ is a time-invariant scalar that controls the unconditional risk and return of the
dynamic portfolio” (Daniel and Moskowitz).
Using a simulation, we chose λ in such a way, so that the in-sample annualized volatility of the
strategy is the same as that of the SPY index over the full sample to make it market neutral.
Specifically, the size of the market hedge is based on the future 42-day (2 month) realized
market beta of the portfolio being hedged. Again, to calculate the beta we use 10 daily lags of
the market return.
Finally, this model can give us our buy/sell signal for each stock and the weights of the portfolio
for maximum sharpe ratio even in times of momentum crashes or high volatility, while being
market neutral. This is important in terms of risk management, as the max drawdown will be
significantly reduced compared to a basic momentum based long short strategy.
