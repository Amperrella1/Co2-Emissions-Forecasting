Forecasting annual CO₂ emissions
================

## Emissions and economic output

The first chart shows the emissions intensity of economic output. The
forecasting models that follow predict annual emissions in million
tonnes.

``` r
knitr::opts_chunk$set(echo = FALSE)
library(forecast)
library(ggplot2)
library(xgboost)

set.seed(42)
df <- read.csv("data/co2-gdp.csv")
df <- df[order(df$country, df$year), ]
countries <- c("United States", "China", "Germany", "Canada", "Bangladesh")

ggplot(df, aes(year, co2 * 1e9 / gdp, colour = country)) +
  geom_line() +
  labs(x = "Year", y = "kg CO₂ per 2011 international dollar", colour = NULL) +
  theme_minimal()
```

![](Report_files/figure-gfm/setup-1.png)<!-- -->

## Ten-year holdout

Each model forecasts 2013–2022 from the end of 2012. GDP for the
forecast years is supplied as an observed input, so forecasts from
models that use GDP are conditional on its realized path. Training
starts at the beginning of each country’s last uninterrupted series so a
one-year lag always spans one calendar year.

## ARIMA and XGBoost

The baseline is ARIMA(3,1,0) with drift. Annual observations have no
within-year seasonal component. XGBoost uses three emissions lags, their
mean and standard deviation, GDP, and calendar year. China and
Bangladesh are also fitted on the log scale. All errors are measured in
million tonnes after converting predictions back to the original scale.

## Forecasts and errors

The China log-scale comparison includes both hybrid specifications. One
adds an XGBoost residual forecast to ARIMA. The other adds an
autoregressive residual forecast to XGBoost.

![](Report_files/figure-gfm/comparison-1.png)<!-- -->![](Report_files/figure-gfm/comparison-2.png)<!-- -->

|     | country       | scale | method                    |    RMSE |     MAE |
|:----|:--------------|:------|:--------------------------|--------:|--------:|
| 1   | Bangladesh    | Level | ARIMA                     |   17.02 |   14.54 |
| 9   | Bangladesh    | Level | XGBoost                   |   32.78 |   29.00 |
| 6   | Bangladesh    | Log   | ARIMA                     |    2.89 |    2.26 |
| 14  | Bangladesh    | Log   | XGBoost                   |   31.87 |   28.07 |
| 10  | Canada        | Level | XGBoost                   |   21.91 |   16.44 |
| 2   | Canada        | Level | ARIMA                     |   35.86 |   26.50 |
| 11  | China         | Level | XGBoost                   |  886.22 |  657.97 |
| 3   | China         | Level | ARIMA                     | 1331.72 | 1243.66 |
| 15  | China         | Log   | XGBoost                   |  893.85 |  666.46 |
| 16  | China         | Log   | XGBoost + AR residuals    |  938.24 |  724.29 |
| 8   | China         | Log   | ARIMA + XGBoost residuals | 4573.20 | 3830.94 |
| 7   | China         | Log   | ARIMA                     | 5287.48 | 4482.65 |
| 12  | Germany       | Level | XGBoost                   |   83.66 |   61.80 |
| 4   | Germany       | Level | ARIMA                     |  116.41 |   93.21 |
| 13  | United States | Level | XGBoost                   |  242.42 |  187.51 |
| 5   | United States | Level | ARIMA                     |  369.94 |  276.68 |
