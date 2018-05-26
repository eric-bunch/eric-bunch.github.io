---
layout: post
title: "Forecasting new home sales in the U.S."
date: 2017-05-25
blurb: We use R to forecast the time series of monthly sales of new one-family houses sold in the USA from 1973 to 1996.
author: Eric Bunch
---


In this post we will forecast the time series <code>hsales</code>, which can be found in the <code>R</code> package <code>fma</code>. The <code>hsales</code> data contains monthly sales of new one-family houses sold in the USA from 1973 to 1996. This analysis will be done in <code>R</code> using the <code>forecast</code> package.


<h2>Visualization and exploration</h2>


The time series plot <code>hsales</code> is below.
<br>
<pre><code>R> plot(hsales)</code></pre>
<br>
<img src="/images/hsales.png" style="width: 100%;" >
<br>
<br>
We can initially see that there is some seasonality in the data. Next, we plot the decomposition of the time series into seasonal, trend, and remainder components.
<br>
<pre><code>R> plot(stl(hsales, s.window="periodic"))</code></pre>
<br>
<img src="/images/hsales_stl.png" style="width: 100%;" >
<br>
<br>

<h2>Transforming the time series</h2>

We will adjust the time series for seasonality by removing the seasonal component.
<br>
<pre><code>
R> hadj <- seasadj(stl(hsales, s.window = "periodic"))
R> tsdisplay(hadj)
</code></pre>

<br>
<img src="/images/tsdisplay_hadj.png" style="width: 100%;" >
<br>
<br>

We will model the trend component (graphed above) of <code>hsales</code>. We will use a seasonal ARIMA model for this, which means we must transform the data into a stationary time series. The high PACF at lag 1 suggests we should difference the data with lag 1.
<br>
<pre><code>R> tsdisplay(diff(hadj))</code></pre>
<br>
<img src="/images/tsdisplay_diff_hadj.png" style="width: 100%;" >
<br><br>

We use the Augmented Dickey-Fuller Test to test for stationarity.

<br>
<pre><code>
R> adf.test(diff(hadj))

Augmented Dickey-Fuller Test

data:  diff(hadj)
Dickey-Fuller = -7.4186, Lag order = 6, p-value = 0.01
alternative hypothesis: stationary

Warning message:
In adf.test(diff(hadj)) : p-value smaller than printed p-value
</code></pre>
<br>
We can see that indeed, <code>diff(hadj)</code> is stationary.

<h2>Forecasting</h2>

The PCAF suggests that there are no large autocorrelations in <code>diff(hadj)</code>, so we will first try ARIMA(1, 0, 0).
<br>
<pre><code>
R> fit <- Arima(diff(hadj), order=c(1, 0, 0))
R> summary(fit)
Series: hadj
ARIMA(1,0,0) with non-zero mean

Coefficients:
         ar1     mean
      0.9013  52.5875
s.e.  0.0255   2.5165

sigma^2 estimated as 18.2:  log likelihood=-789.02
AIC=1584.04   AICc=1584.13   BIC=1594.89

Training set error measures:
                      ME     RMSE      MAE        MPE     MAPE      MASE        ACF1
Training set -0.05203778 4.251127 3.325061 -0.8537709 6.641165 0.4037355 -0.02679891
</code></pre>
<br>
We also try other models close to the initial one chosen and compare AICc values to see which gives the lowest. Out of ARIMA(1, 0, 0), ARIMA(1, 1, 0), and ARIMA(1, 0, 1), the model with the lowest AICc is ARIMA(1, 0, 1), so we will choose this one.
<br>
<pre><code>
R> fit <- Arima(diff(hadj), order=c(1, 0, 1))
R> summary(fit)
Series: diff(hadj)
ARIMA(1,0,1) with non-zero mean

Coefficients:
         ar1      ma1     mean
      0.6672  -0.7997  -0.0173
s.e.  0.1248   0.0984   0.1575

sigma^2 estimated as 18.65:  log likelihood=-788.17
AIC=1584.34   AICc=1584.48   BIC=1598.79

Training set error measures:
                     ME     RMSE      MAE      MPE     MAPE     MASE       ACF1
Training set -0.0149392 4.294922 3.360814 99.24458 115.8192 0.686308 0.02646853
R> plot(forecast(fit, h=24))
</code></pre>

<br>
<img src="/images/arima_1_0_1.png" style="width: 100%;" >
<br><br>

<h2>Testing residuals for white noise</h2>

We need to test whether or not the residuals of the fit are white noise. We will plot the histogram of the residuals as well as the line plot first.

<img src="/images/fit_residuals_hist.png" style="width: 100%;">

<br><br>

<img src="/images/fit_residuals_plot.png" style="width: 100%;">

<br><br>


We will also use the Box-Ljung test to test if the residuals are white noise.

<pre><code>
R> Box.test(residuals(fit), type = "Ljung")

  Box-Ljung test

data:  residuals(fit)
X-squared = 0.19407, df = 1, p-value = 0.6596
</code></pre>

The p-value indicates that we can reject the null hypothesis, and assume the residuals are white noise. This means we have a decent fit to the time series, and we will proceed with reverse transforming the forecast.


<h2>Reverse transformation</h2>

We have the forecast of the differenced, seasonally adjusted <code>hsales</code> time series. The transformations were not too outlandish, so the forecast interpretation is not terribly difficult, but we wish to  reverse transform this forecast and plot it. To do that, we will first fit the seasonal part of hsales with a simple <code>ETS</code> model, and add this to the reverse transformed forecast of <code>diff(hadj)</code>.
<br>
<pre><code>
R> hseas <- stl(hsales, s.window="periodic")$time.series[,'seasonal']
R> sfit <- ets(hseas)
R> summary(sfit)
ETS(A,N,A)

Call:
 ets(y = hseas)

  Smoothing parameters:
    alpha = 0.0418
    gamma = 0.048

  Initial states:
    l = 0
    s=-12.4225 -9.0642 -2.4584 -1.3744 2.9153 1.5529
           4.7527 7.3438 7.9677 9.07 -1.2304 -7.0525

  sigma:  0

      AIC      AICc       BIC
-18629.63 -18627.78 -18575.38

Training set error measures:
                     ME        RMSE          MAE           MPE         MAPE MASE        ACF1
Training set 1.8571e-17 1.11224e-16 5.571301e-17 -4.056508e-15 4.056508e-15  Inf -0.02878259

R> plot(forecast(sfit, h=6))
</code></pre>

<br>
<img src="/images/ets_hseas.png" style="width: 100%;" >

<br><br>

We then plot the original time series <code>hsales</code> as well as the transformed forecast for 24 months.
<br>
<pre><code>
R> plot(hsales)
R> lines(diffinv(forecast(fit, h=24)$mean) + forecast(sfit, h=24)$mean + tail(hsales[-1], n=1), col='red')
</code></pre>
<br>
<img src="/images/24_months_forecast_hsales.png" style="width: 100%;" >
<br><br>
