Time Series Algorithm Comparison

tldr;
This project compares several forecasting approaches—including decision trees, random forests, and Auto ARIMA—on a seasonal electricity time series to evaluate which models generalize best out-of-sample. The analysis shows how model choice, feature engineering, and variance transformations affect forecasting accuracy, highlighting the value of disciplined model comparison in time-dependent data problems.

Why this project matters

In many real-world analytics problems, the hardest question is not how to train a model but which modeling approach is actually appropriate for the structure of the data. Time-dependent data often breaks the assumptions of ordinary regression, yet many forecasting projects default to a single method without testing alternatives.

I built this project to compare several common forecasting approaches on a dataset with clear temporal dependence and seasonality. Instead of assuming a particular method would work best, I evaluated multiple approaches using a strict time-ordered train/test split and compared their out-of-sample performance.

The goal was to answer a practical question that comes up frequently in product and operations work: when you need to forecast behavior over time, which tools actually generalize well, and what kinds of preprocessing or feature choices make the biggest difference.

This kind of problem appears in many applied settings such as demand forecasting, call volume prediction, product usage trends, and marketplace activity. The exercise reflects the way I typically approach analytics problems in practice: start by understanding the data-generating process, test reasonable modeling alternatives, and evaluate them on holdout data rather than relying on theoretical preference.

Project overview

This project compares several modeling strategies for predicting monthly electricity production. The dataset spans more than three decades and exhibits strong seasonal patterns as well as longer-term changes in level.

The models evaluated include:

Decision tree regression
Random forest regression
Random forest with lag features
Auto ARIMA
Auto ARIMA with a log transformation

All models were evaluated on an out-of-sample test set using RMSE and R².

One of the main goals was to see whether classical time-series methods outperform tree-based machine learning models in this setting, and to understand how much preprocessing decisions influence results.

Dataset

The data consists of monthly electricity production measurements from January 1985 through January 2018.

This dataset is useful for experimentation because it contains:

long-term trends
repeating seasonal patterns
autocorrelation across observations

Those characteristics make it a realistic forecasting challenge and a good test case for comparing classical time-series techniques with machine learning approaches.

Modeling approach

The analysis follows a time-aware modeling workflow designed to avoid leakage and to evaluate models fairly.

First, the data was split chronologically into an 80 percent training set and a 20 percent test set. This preserves the temporal ordering of the data and ensures the models are evaluated on observations that occur strictly after the training period.

Next, residual autocorrelation was examined using the Ljung–Box test. This step confirmed that the time series exhibits significant temporal dependence, which helps explain why simple regression-style approaches can struggle.

Several modeling strategies were then implemented.

Decision tree

A regression tree was fit using year and month as predictors. This served as a transparent baseline that captures some seasonal structure but has limited ability to smooth across patterns.

Random forest

An ensemble of decision trees was trained using bootstrap sampling and random feature selection. The forest aggregates many trees to produce a more stable prediction than any individual tree.

Two configurations were explored, including a large forest with 500 trees and a smaller ensemble, to examine how performance scales with model complexity.

Random forest with lag features

Lagged predictors were added to explicitly encode temporal memory. These included values from the previous month, two months prior, and twelve months prior. This tested whether giving the model direct access to past observations would improve performance.

Auto ARIMA

A classical time-series forecasting model was fit directly to the series. Auto ARIMA automatically searches over possible model configurations and selects the best candidate based on information criteria.

Logged Auto ARIMA

Because variance appeared to increase over time, a log transformation was applied before fitting the ARIMA model. This step stabilizes variance and can improve forecasting performance when the scale of the series changes.

Results

The models were compared using out-of-sample RMSE and R².

The random forest model produced the best overall forecasting performance on the holdout data. It substantially improved upon the single decision tree baseline and captured seasonal structure effectively using only calendar features.

The default Auto ARIMA model performed poorly in comparison, but applying a log transformation improved its performance significantly and brought it much closer to the machine learning models.

Interestingly, adding lagged predictors to the random forest did not improve results in this dataset. Because the sample size was modest and lag features remove early observations, the additional features likely introduced more noise than useful signal.

Key takeaways

Several lessons emerged from the comparison.

Ensemble models can capture seasonal patterns effectively even with relatively simple input features. The random forest model performed well despite using only year and month as predictors.

Feature engineering should always be validated rather than assumed. Adding lag features did not improve performance in this case, illustrating that additional complexity does not always lead to better forecasts.

Preprocessing decisions can be as important as model choice. The improvement observed after log-transforming the data highlights how variance stabilization can materially affect forecasting accuracy.

Finally, disciplined model comparison is often more valuable than relying on a single modeling framework. In many applied settings, the best approach only becomes clear after evaluating multiple reasonable alternatives.

Tools used

The analysis was implemented in R using packages including tidyverse, lubridate, forecast, randomForest, rpart, caret, and ggplot2.

Repository contents

The repository contains the full analysis code along with the presentation used to summarize the modeling comparison and results.
