# Predicting Housing Absorptions In The City of Toronto

## Project Overview

Housing absorption is the rate at which homes in a particular market and timeframe are sold. It is an important metric that can be used for assessing a housing markets supply and demand levels.

For this project, I wanted to see whether I can build a regression model that accurately predicts, and then forecasts, the number of monthly housing absorptions in the City of Toronto. This is a time series forecasting type of prediction. To keep it simple, I wanted to try and predict the total number of absorptions across all dwelling types (single, semi-detached, row, and apartment). However, it is very well possible to predict the number of absorptions by dwelling type, or even of another city, if one wanted to.

## The Inspiration And Business Case

The idea for this project came to me when I was thinking about assessing the real estate market share in Toronto, and how a company can set absorption targets so that they achieve a desired market share. For example, let's say I was a realtor whose goal is to achieve a certain percent of the market sales. If I could accurately forecast the number of absorptions over a certain time period, I can then figure out how many sales I need to make in that time period to achieve my desired market share.

## The Approach

I tried out three methods for predicting absorptions:

1) A traditional machine learning regression approach which uses various economic data points (a multivariate model). My thinking was that absorptions can be predicted by using a combination of monthly economic data points like the number of housing completions, unemployment rate, borrowing rate, the year over year change in home prices, etc. 

2) Traditional univariate time series forecasting methods such as simple moving averages and ARMA.

3) Using Facebook's Prohpet model.

A link to the full notebook with my code and breakdown of the preprocessing steps can be found [here](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/Toronto%20Housing%20Absorptions.ipynb).

## The Data

Housing absorption data was obtained from the Canada Mortgage and Housing Corporation (CMHC) information portal. A link to this information portal can be found [here](https://www03.cmhc-schl.gc.ca/hmip-pimh/en/TableMapChart?id=2270&t=3).

The economic data points that were used in the multivariate model were obtained using the Statistics Canada (StatsCan) API. This API provides a connection to the StatsCan data portal, which contains thousands of economic datasets. With the exception of a few features, most of the datapoints that I gathered for this project are specific to the Toronto metropolitan area. A link to the StatsCan data portal can be found [here](https://www150.statcan.gc.ca/n1//en/type/data?MM=1#tables). The documentation for the StatsCan API can be found [here](https://stats-can.readthedocs.io/en/latest/).

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

If a period of only one month is required to be forecasted, then it is recommended to use a simple twelve-month moving average given its simplicity and relative accuracy to a more complicated machine learning approach. If a larger period is required to be forecasted then it is recommended to keep the forecasting window as small as possible, and use either ARMA (7,7) or Prophet. However, it must be noted that the autoregressive (p) and moving average (q) components in the ARMA(7,7) model, while they are statistically significant, they may be picking up on noise in the data set which could lead to untrustworthy results.

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/total_absorptions.png)

## Lessons That I Learned

In no particular order, the biggest lessons that I learned through working on this project are: 

- Time series forecasting problems are challenging. If there is no clear trend, seasonality, or if the data is noisy, then it might be too complicated to accurately forecast. There are a lot of resources out there that teach time series forecasting methods, and almost all of them are on trivial "toy" datasets. Do not assume that these methods are going to work on every problem you face.

- Simple solutions are sometimes the best solutions. Try not to over complicate the problem. For example, I spent a lot of preprocessing time working on the multivariate machine learning model to later only find out that a simple tweleve month moving average was more accurate, versatile, and less complicated. In addition, it would be easier to explain how this approach works to managment.

- Be careful when trying to impute a large number of missing values using an intuitive multivariate imputer. I spent a lot of time trying to impute missing values for features that had over 60% of their values missing. The problem with this approach is that it did not yield realistic results and led to a large amount of multicollinearity as features would start mirroring each other.

- The Statistics Canada API is a great and easy to use resource that allows for the retrieval of data from the Statistics Canada data portal. 

## Other Areas To Explore

During the data exploration process I observed that housing completions and absorptions are highly correlated. From what I observed, I suspect that there would be a strong correlation between housing starts and a lag value of absorptions, where the lag value would represent the construction duration. In other words, you can probably forecast what absorptions would be if you just shift the number of housing starts forward by the average construction duration.  

## The Multivariate Machine Learning Approach

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/acf.png)

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/pacf.png)

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/lasso.png)

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/lasso_coeffs.png)

## Moving Average Approach

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/sma3.png)

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/sma12.png)

## ARMA

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/arma.png)

## Prophet

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/prophet_test.png)

![](https://github.com/RoryAJames/Toronto-Housing-Absorptions/blob/main/images/prophet_forecast.png)
