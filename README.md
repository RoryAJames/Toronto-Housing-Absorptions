# Predicting Housing Absorptions In The City of Toronto

## Project Overview

Housing absorption is the rate at which homes in a particular market and timeframe are sold. It is an important metric that can be used for assessing a housing markets supply and demand levels.

For this project, I wanted to see whether I can build a regression model that accurately predicts, and then forecasts, the number of monthly housing absorptions in the City of Toronto. This is a time series forecasting type of prediction. To keep it simple, I wanted to try and predict the total number of absorptions across all dwelling types (single, semi-detached, row, and apartment). However, it is very well possible to predict the number of absorptions by dwelling type, or even of another city, if one wanted to.

## The Inspiration And Business Case

The idea for this project came to me when I was thinking about assessing the real estate market share in Toronto, and how a company can set absorption targets. For example, let's say I was a realtor whose goal is to achieve a certain percent of the market sales. If I could accurately forecast the number of absorptions over a certain time period, I can then figure out how many sales I need to make in that time period to achieve my desired market share.

## The Approach

I tried out three methods for predicting absorptions:

1) A traditional machine learning regression approach which uses various economic data points (a multivariate model). 

2) Traditional univariate time series forecasting methods such as simple moving averages and autoregressive moving average (ARMA).

3) Using Facebook's Prohpet model.

A link to the full notebook with my code, exploratory data analysis, and breakdown of the preprocessing steps I applied can be found [here](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/Toronto%20Housing%20Absorptions.ipynb).

## The Data

Housing absorption data was obtained from the Canada Mortgage and Housing Corporation (CMHC) information portal. A link to this information portal can be found [here](https://www03.cmhc-schl.gc.ca/hmip-pimh/en/TableMapChart?id=2270&t=3).

The economic data points that were used in the multivariate model were obtained using the Statistics Canada (StatsCan) API. This API provides a connection to the StatsCan data portal, which holds thousands of economic datasets. A link to the StatsCan data portal can be found [here](https://www150.statcan.gc.ca/n1//en/type/data?MM=1#tables). The documentation for the StatsCan API can be found [here](https://stats-can.readthedocs.io/en/latest/).

## Model Performance

Each model was trained on 223 months of data from the periods between April 2001 to October 2019. A test set of 24 months, from November 2019 to October 2021, was used to evaluate each models prediction performance. Since this is a time series type of prediction, the data was not split randomly but rather chronologically in order to avoid data leakage.

Model performance was measured by looking at the mean absolute error, root mean squared error, and mean absolute percent error. Below is a breakdown of each models performance:

| Model                                  | Mean Absolute Error | Root Mean Squared Error | Mean Absolute Percent Error |
| -------------                          |:-------------:      | :-----:                 | :-----:                     |
| Lasso With 0.01 Alpha                  | 640                 |        926              | 23%                         |
| Three Month Moving Average             | 713                 |        969              | 28%                         |
| Twelve Month Moving Average            | 630                 |        859              | 25%                         |
| ARMA (7,7)                             | 656                 |        856              | 30%                         |
| Prophet                                | 627                 |        839              | 27%                         |

## Key Findings

Housing absorptions is a noisy data set, with no clear trend or seasonality. The data seems to fluctuate randomly and there are multiple changepoints, which makes forecasting very challenging. None of the tested forecasting methods are perfect and should not be taken for granted. Depending on the business needs, and the number of periods that need to be forecasted, decision makers should select an appropriate forecasting method.

If a period of only one month is required to be forecasted, then it is recommended to use a simple twelve-month moving average given its simplicity and relative accuracy to the more complicated machine learning approach. If a larger period is required to be forecasted then it is recommended to keep the forecasting window as small as possible, and use either ARMA (7,7) or Prophet. However, it must be noted that the autoregressive (p) and moving average (q) components in the ARMA(7,7) model, while they are statistically significant, they may be picking up on noise in the data set which could lead to untrustworthy results.

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/total_absorptions.png)

## Lessons That I Learned

The biggest lessons that I learned through working on this project are: 

- Time series forecasting problems are challenging. If there is no clear trend, seasonality, or if the data is noisy, then it might be too complicated to accurately forecast. There are a lot of resources out there that teach time series forecasting methods, and almost all of them are on trivial "toy" datasets, where the results are rather easy to produce. Do not assume that these methods are going to work on every problem you face.

- Simple solutions are sometimes the best solutions. Try not to over complicate the problem. For example, I spent a lot of preprocessing time working on the multivariate machine learning model to later only find out that a simple twelve month moving average was more accurate, versatile, and less complicated. In addition, this model is far easier to explain to people who may not be versed in statistics or machine learning.

- Be careful when trying to impute a large number of missing values using an intuitive multivariate imputer. I spent a lot of time trying to impute missing values for features that had over 60% of their values missing. The problem with this approach is that it did not yield realistic results and led to a large amount of multicollinearity, as features would start mirroring each other.

- The Statistics Canada API is a great and easy to use resource that allows for the retrieval of data from the Statistics Canada data portal. 

## Other Areas To Explore

During the data exploration process I observed that housing completions and absorptions are highly correlated. From what I observed, I suspect that there would be a strong correlation between housing starts and a lag value of absorptions, where the lag value would represent the construction duration. In other words, you can probably forecast absorptions if you just shift the number of housing starts forward by the average construction duration.  

## The Multivariate Machine Learning Approach

My original thinking for this project was that housing absorptions in Toronto can be predicted by using a combination of monthly economic data points. Most of the data points I gathered are specific to the metropolitan Toronto area, along with a couple of national data points as well. I intuitively started with the following features:

- The number of housing units created in the metropolitan Toronto area.
- The inventory of completed and unabsorbed housing units in the metropolitan Toronto area.
- Toronto's population.
- Toronto's unemployment rate.
- The five-year term conventional mortgage rate.
- Toronto's inflation rate calculated by CPI year over year.
- Toronto's year over year change in the new home price index.
- The total construction investment into Toronto's residential buildings.
- The total retail trade in dollars for the metropolitan Toronto area.
- The year over year return of the Toronto Stock Exchange.
- The year over year change in the M2 money supply.

It was discovered in the exploratory data anlysis that residential building construction investment, along with retail trade in dollars, had a significant amount out missing values. These features were removed as result. It was later discovered that the number of housing units created and total absorptions had a very strong correlation with eachother. The housing units created feature was removed to avoid multicollinearity. Most of the remaining features were right skwed so a log transformation was applied.

In order to turn my time series problem into a supervised machine learning problem I had to lag all of the remaining features by a point in time, and subsequently create another feature which was the lag value of total absorptions itself. Using autocorrelation (ACF) and partial autocorrelation (PACF) plots, I determined that a value of one lag would be appropriate, as it showed to be statistically significant on both plots.

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/acf.png)

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/pacf.png)

For the model building, I wanted to see how regularized and ensemble methods compared to each other. This was because some of the remaining features still showed moderate correlations with eachother. A pipeline dictionary containing various models, along with a hyperparameter dictionary for each model, was passed through GridSearchCV. Each models performance was measured using the mean absolute error, root mean squared error, and mean absolute percentage error with five-fold cross validation.

The grid search showed that a Lasso model with an alpha value of 0.01 had the best performance metrics. A seperate instance of this model was then created and applied on the test set to visualize the predictions. I will note that a 0.01 alpha value is a rather low for a Lasso model, and not that far off from what a linear regression model would produce.

While the Lasso model generally followed the trend of absorptions, it typically under predicted what the actual absorptions were. To me, it appeared that the predictions closely resembled that of a large moving average window. This was later tested in the univariate section.

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/lasso.png)

Since my original assumption for the project was that housing absorptions can be predicted using various economic features, I decided it would be appropriate to visualize the coefficients of the Lasso model. In addition, I knew that the Lasso model does feature selection behind the scenes through penalizing the coefficients, and would eliminate features that do not have a material impact on the dependant variable.

The year over year CPI rate (inflation), unemployment rate, and inventory of completed and unabsorbed housing units were eliminated from the Lasso model. Interestingly, the year over year new home price index value displayed the largest coefficient. This made sense, as it was observed in the exploratory data analysis that the trend line in total absorptions closely followed the year over year trend in the new home price index.

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/lasso_coeffs.png)

While the Lasso model performed decently, there are limitations to this approach. Firstly, it is fairly complicated and does not easily allow for forecasting into periods beyond the data frame. Secondly, it is limited by depending on StatsCan to update the economic data. Sometimes it can take a while for the latest economic data to become available. Based on the way the model is set up, it only allows for forecasting one month ahead, and only if data for all of the features is available. In most cases, by the time the data does become available the forecasts might already be outdated.

## Moving Average Approach

Since the Lasso predictions appeared to follow the mean value of total absorptions, I decided it would be appropriate to compare the performance of a three month moving average to a twelve month moving average.

The three month moving average approach was a lot more responsive to the fluctuations in total absorptions. However, it did not have better performance metrics than the Lasso model, as some points had rather large residuals.

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/sma3.png)

The twelve month moving average approach did perform better than the Lasso model on a mean absolute error, and root mean squared error basis. The predictions appear very similar to the Lasso model too, in that they follow the overall trend of total absorptions.

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/sma12.png)

## ARMA

The reason for using an ARMA model over an integrated ARIMA model is that absorptions is a stationary dataset. No differencing was required. This was observed through the ACF and PACF plots which showed a drastic decrease to zero. To be sure, a Dickey-Fuller test was conducted to validate the assumption.

Multiple instances of the ARMA model were tested. From what I observed in the moving average portion, a larger moving average window resulted in better prediction performance. A lag of seven was the largest value that showed to be significant from the ACF plot, and so it was used. In the end, an ARMA(7,7) model with exclusive p, and q components showed to have statistically significant coefficients and a high Ljung-Box statistic.

Visually, it is apparent that the predictions of the ARMA (7,7) stay close to the constant value but do have greater month over month fluctuations when compared to the twelve month moving average.

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/arma.png)

## Prophet

The reason for using Prophet is that it is a well known package in the Data Science community for being able to handle momentum shifts in the data. Since I had observed that absorption data has multiple changepoints, and that the trend fluctuates frequently over time, I figured it would be a good test for Prophet.

The first few months of predictions using Prophet were rather impressive. While the model did over predict what the actual absorptions were, it was the best model when it comes to matching the overall fluctuations in the data. From my original business use case, I would say it is more advantageous to over predict absorptions than to under predict them. After a certain point the Prophet predictions look like they revert to the mean as well.

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/prophet_test.png)

Given it's relative performance, and easy implementation, Prophet was used as the model of choice for forecasting absorptions in Toronto. A six month forecasting window was used, and an upper and lower bound prediction was added as well. The default 80% uncertainty levels were used for the lower and upper bounds.

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/prophet_forecast.png)
