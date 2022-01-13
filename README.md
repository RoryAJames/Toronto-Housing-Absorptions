# Predicting Housing Absorptions In The City of Toronto

## Project Overview

Housing absorption is the rate at which homes in a particular market and timeframe are sold. It is an important metric that can be used for assessing a housing markets supply and demand levels.

For this project, I wanted to see whether I can build a regression model that accurately predicts, and then forecasts, the number of monthly housing absorptions in the City of Toronto. To keep it simple, I wanted to try and predict the total number of absorptions across all dwelling types (single, semi-detached, row, and apartment). However, it is very well possible to predict the number of absorptions by dwelling type, or even of another city, if one wanted to.

## The Inspiration And Business Case

The idea for this project came to me when I was thinking about assessing the real estate market share in Toronto, and how a company can set absorption targets so that they maintain a desired market share. For example, let's say I was a realtor whose goal is to maintain a certain percent of the market sales. If I could accurately forecast the number of absorptions over a certain time period, I can then figure out how many sales I need to make in that time period to achieve my desired market share.

## The Approach

I tried out three methods for predicting absorptions:

1) A traditional machine learning regression approach which uses various economic data points (a multivariate model). My thinking was that absorptions can be predicted by using a combination of economic data points like unemployment rate, borrowing rate, home prices, etc. 

2) Traditional univariate time series forecasting methods such as simple moving averages and ARMA.

3) Using Facebook's Prohpet model.

## Model Performance

Model performance was measured by looking at the mean absolute error, root mean squared error, and mean absolute percent error.

| Model                                  | Mean Absolute Error | Root Mean Squared Error | Mean Absolute Percent Error |
| -------------                          |:-------------:      | :-----:                 | :-----:                     |
| Lasso With 0.01 Alpha                  | 640                 |        926              | 23%                         |
| Three Month Moving Average             | 713                 |        969              | 28%                         |
| Twelve Month Moving Average            | 630                 |        859              | 25%                         |
| ARMA (7,7)                             | 656                 |        856              | 30%                         |
| Prophet                                | --                  |        --               | --                          |

## Key Findings

Still a work in progress...

## What I Learned

I am still learning!

## The Multivariate Machine Learning Approach

## Simple Three Month Moving Average

## Simple Twelve Month Moving Average

## ARMA

## Prophet
