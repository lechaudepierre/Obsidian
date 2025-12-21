## Overview

Time series forecasting estimates how sequentially observed data will continue into the future, often with uncertainty indicators.

## Types of Forecasting Models

### 1. Explanatory Models

- Use predictor variables to capture reasons for change
- Example: `Electricity Demand = f(temperature, economy, population, time of day, day of week, error)`
- Similar to classification and regression models

### 2. Time Series Models

- Predict future based on past values only (no external variables)
- Example: `ED(t+1) = f(ED(t), ED(t-1), ED(t-2), ..., error)`

### 3. Mixed Models

- Combine both approaches
- Example: `ED(t+1) = f(ED(t), temperature, time of day, day of week, error)`

## Time Series Components

**Trend**: Long-term increase or decrease in data

**Seasonality**: Patterns affected by seasonal factors (time of year, day of week) with fixed, known periods

**Cycles**: Fluctuations without fixed frequency, often related to economic conditions (usually ≥2 years)

**Remainder**: What's left after removing trend and seasonal components

## Decomposition Methods

### Additive Decomposition

`y(t) = S(t) + T(t) + R(t)`

### Multiplicative Decomposition

`y(t) = S(t) × T(t) × R(t)`

### Pre-processing Adjustments

- **Calendar adjustments**: Convert to daily rates (account for varying days/work days)
- **Population adjustments**: Convert to per-capita values
- **Inflation adjustment**: Adjust prices to constant year values

### Classical Decomposition Steps

1. Compute trend-cycle using moving averages (m-MA or 2×m-MA)
2. Calculate detrended series: `y(t) - T(t)`
3. Compute seasonal component by averaging detrended values for each season
4. Calculate remainder: `R(t) = y(t) - T(t) - S(t)`

**Note**: STL (Seasonal-Trend decomposition using Loess) is commonly used in practice

## Simple Forecasting Methods

**Mean Method**: Future values = average of historical data  
`y(T+h) = (y₁ + ... + y(T)) / T`

**Naive Method**: Future values = last observed value  
`y(T+h) = y(T)`

**Seasonal Naive**: Future values = last observed value from same season

**Drift Method**: Extends trend from first to last observation  
`y(T+h) = y(T) + h × (y(T) - y₁)/(T - 1)`

## Stationarity

### Definition

A stationary time series has statistical properties (mean, variance) that don't vary over time.

**White Noise**: Strongest form - zero mean, constant variance, zero covariance at any lag

### Why Stationarity Matters

- Forecasting performs better on stationary series
- Statistical parameters remain consistent across time windows
- Non-stationary series should be converted to stationary before forecasting

### Differencing

Convert non-stationary to stationary by replacing values with differences:

- **First-order**: `y'(i) = y(i) - y(i-1)`
- **Second-order**: `y''(i) = y'(i) - y'(i-1) = y(i) - 2×y(i-1) + y(i-2)`

Can be combined with log-scale transformation.

## Autoregressive (AR) Models

### Autocorrelation

- Measures correlation between values at different time lags
- Range: [-1, 1]
- Typically positive for small lags (nearby values are similar)
- Visualized using ACF (Autocorrelation Function) plots

### AR(p) Model

Predicts current value as linear combination of p previous values:

`y(t) = c + a₁×y(t-1) + a₂×y(t-2) + ... + a(p)×y(t-p) + ε(t)`

Where:

- p = number of lagged values (window length)
- a(i), c = learned parameters
- ε(t) = error term

## Moving Average (MA) Models

### MA(q) Model

Relates current value to previous forecast errors (shocks):

`y(t) = c + ε(t) + θ₁×ε(t-1) + θ₂×ε(t-2) + ... + θ(q)×ε(t-q)`

Where:

- q = number of lagged errors
- ε(t-i) = deviations from predicted values (white noise/shocks)
- θ(i) = learned parameters

**Key Difference**: AR relates to previous values; MA relates to previous forecast errors

## ARMA and ARIMA Models

### ARMA(p, q)

Combines AR and MA models for stationary series:

`y(t) = c + Σa(i)×y(t-i) + ε(t) + Σθ(j)×ε(t-j)`

### ARIMA(p, d, q)

ARMA with integrated differencing for non-stationary series:

- p = AR order (number of lagged values)
- d = differencing order (0, 1, or 2)
- q = MA order (number of lagged errors)

**ARIMA is the most general model for time series that can be made stationary.**

## Prophet Model (Facebook, 2019)

Additive regression model with four components:

`y(t) = g(t) + s(t) + h(t) + ε(t)`

Where:

- **g(t)**: Piecewise linear/logistic growth trend with automatic changepoint detection
- **s(t)**: Periodic changes (seasonality) using Fourier series
- **h(t)**: Holiday effects (user-provided important dates)
- **ε(t)**: Error term (normally distributed)

**Design Goals**: Ease of use and speed for forecasting at scale

## Key Takeaways

1. Choose forecasting approach based on data characteristics and available information
2. Decompose series to understand components before modeling
3. Convert to stationary series when possible for better forecasts
4. AR models use past values; MA models use past errors
5. ARIMA is versatile for many time series after achieving stationarity
6. Modern tools like Prophet offer user-friendly alternatives for production use