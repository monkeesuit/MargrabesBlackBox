
Please use SPY and QQQ as one can readily get price history for those.

## Specification

Assume that we have a “black box” model which specifies the market inputs and for each set of inputs
gives us an output:


$$v_{output} = V(input_1, input_2, ...)$$

For the sake of this demo as a “black box” model we chose the Margrabe model for computing the value
of the call option on the spread of two asset prices. At expiration the payout of this option:

$$Payout_{expiry} = max(s_1 - s_2, 0)$$

Thus, the black box model, Margrabe model, requires the current values of the assets $S_1$, $S_2$ the
volatilities $\sigma_1$, $\sigma_2$ correlation $\rho_{1,2}$ value time and expiry time, $t$, $T$. Everywhere below we assume that
the interest rate $r = 0$. Then, the input/output relation is

$$v_{spreadOption} = M(S_1, S_2, \sigma_1, \sigma_2, \rho; t, T)$$

with the first 5 variables being the market inputs.

## Main Loop

We will now generate 100 test scenarios.

### Test Scenario $i$

Each scenario is specified by the historical value date $t_i$ and expiry date $T_i = t_i + 3 \space months$ (3 months
are chosen arbitrarily; it can be 6 months or a year). For example, $t_i$ is Sep 1, 2015 and $T_i$ is Nov 30,
2015.

Once the dates of the scenario are specified, retrieve the time series for $S_1$ and $S_2$:

$$S_1(t_k) \space S_2(t_k), \space t_i \le t_k \le T_i$$

Now, calculate the volatilities $\sigma_1$, $\sigma_2$, as historical realized volatilities between $t_i$ and $T_i$ . Use all 3 months
of historical data to compute these vols. Use the same data to compute the correlation $\rho$.

The above step for computing vols and correlations is very important. It ensures that the implied vols
are correct and are not the source of errors.

Once we have the time series for all inputs, compute the time series

$$v_k = M(S_1(t_k),\,S_2(t_k),\, \sigma_1,\, \sigma_2,\, \rho;\ t_k,\, T_i), \quad t_k = t_i, ..., T_i - 1 $$

Using standard central finite difference approach compute Greeks:
$$\delta_k, \gamma_k, Vega_k, \theta_k$$

Compute daily PnLs and Greek PnLs:

$$\Delta^{value}_k = v_{k+1} - v_k$$

$$\Delta^{delta}_k = \delta_{1,k}(S_1(t_{k+1}) - S_1(t_k)) + \delta_{2,k}(S_2(t_{k+1}) - S_2(t_k))$$

$$\Delta^{gamma}_k = .5\gamma_{11,k}(S_1(t_{k+1}) - S_1(t_k))^2 + \gamma_{12,k}(S_1(t_{k+1}) - S_1(t_k))(S_2(t_{k+1}) - S_2(t_k)) + .5\gamma_{22,k}(S_2(t_{k+1}) - S_2(t_k))^2$$

$$\Delta^{vega}_k = Vega_{1,k}(\sigma_1(t_k+1) - \sigma_1(t_k)) + Vega_{2,k}(\sigma_2(t_k+1) - \sigma_2(t_k)) = 0$$

The vega term is zero because the vols don’t change from day to day, as we fixed them at the beginning
to be the realized vols.

$$\theta_k = M(S_1(t_k), S_2(t_k), \sigma_1, \sigma_2, \rho; t_{k+1}, T_i) - M(S_1(t_k), S_2(t_k), \sigma_1, \sigma_2, \rho; t_{k}, T_i)$$

This completes the calculations for the Test Scenario i. In this scenario we have generated vectors of 3
months of option PnLs and 4 Greek PnL vectors (not counting Vega).

We then repeat this test for different dates $t_i$, $i = 1, ... ,100$ accumulating 100 vectors of option PnLs
for each test and corresponding Greek PnL vectors.

This completes the computational stage. On the next stage we will do the postprocessing of this data
and the result presentation.


## Output

For each scenario $i$, you should generate 3 months of option PnL and Greek PnL (each day of
these three months is indexed by k).

Please put your results in the following format:
$i,\, k,\, S_{1,k},\, S_{1,k+1},\, S_{2,k},\, S_{2,k+1},\, Strike,\, \sigma_{1,k},\, \sigma_{1,k+1},\, \sigma_{2,k},\, \sigma_{2,k+1},\, \rho,\, v_k,\, v_{k+1},\, \delta_1,\, \delta_2,\, \gamma_1,\, \gamma_2,\, Vega_1,\, Vega_2,\, PnL,\, All\ 4\ GreekPnL\ terms\ (see\ description\ above)$