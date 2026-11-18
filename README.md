# Forecasting annual CO₂ emissions

How accurately can ten years of emissions be forecast from a country's historical emissions and its GDP path? This R project compares ARIMA and XGBoost for the United States, China, Germany, Canada, and Bangladesh. It also tests two ways to combine the models for China.

[View the forecast plots and model scores](Report.md)

## Analysis

[Final.Rmd](Final.Rmd) plots emissions per unit of GDP, then forecasts **total annual CO₂ emissions**. The level-scale comparison covers all five countries. A second comparison fits models to log emissions for China and Bangladesh. In the China analysis, each hybrid uses one model to forecast emissions and the other to correct its residuals.

The common holdout is **2013–2022**. Each model trains on the continuous annual history available through 2012. All ten predictions are made from that origin. XGBoost uses earlier predictions to build subsequent emissions features and takes the observed GDP path as an input. Its errors therefore describe a forecast conditional on known GDP. The report gives RMSE and MAE in million tonnes for each country, scale, and model.

## Results

On the level scale, XGBoost had lower holdout RMSE in Canada, China, Germany, and the United States. Bangladesh had the lowest error with log-scale ARIMA (RMSE 2.89 Mt). For China, adding XGBoost residuals improved log-scale ARIMA (RMSE fell from 5,287 to 4,573 Mt), but the level-scale XGBoost forecast was more accurate (886 Mt). These comparisons cover a single ten-year holdout.

## Data

[data/co2-gdp.csv](data/co2-gdp.csv) is a 722-row extract of the supplied Our World in Data emissions dataset. It contains the five study countries and the four fields used in the analysis. Rows with missing emissions or GDP were excluded. The exploratory chart uses all available pairs, while the forecasts use consecutive annual observations.

[data/codebook.csv](data/codebook.csv) records the definitions, units, and original provider for each field. Emissions exclude land-use change. GDP is adjusted for inflation and purchasing power and expressed in 2011 international dollars. [Our World in Data source repository](https://github.com/owid/co2-data)

## Run the analysis

Open R in the repository root. Install the packages once, then render the report. R Markdown requires Pandoc, which is bundled with RStudio.

```r
install.packages(c("forecast", "ggplot2", "xgboost", "knitr", "rmarkdown"))
rmarkdown::render("Final.Rmd", output_file = "Report.md")
```

The report was rendered with R 4.4.3, forecast 9.0.2, and xgboost 2.1.4. The rendered report contains the emissions-intensity chart, forecast comparisons, and error table.

## Reproducibility notes

This edition uses the models and study countries from the original final project. The rolling features end one year before the prediction target, and hybrid training residuals are matched to their feature years. All models share the same forecast cutoff. These changes affect performance estimates, so results from earlier drafts are not carried forward.
