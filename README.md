# Forecast-bikeshare-demand-using-time-series-models-in-R
The analysis of the bike share system's transition of traits during a duration of time.

---
title: "Forecast daily bike rental demand using time series models"
date: "17th March 2025"
output: time_series_analysis.html_document
author: "Isaac Orimadegun"
---

# About Data Analysis Report

This RMarkdown file contains the report of the data analysis done for the project on forecasting daily bike rental demand using time series models in R. It contains analysis such as data exploration, summary statistics and building the time series models. The final report was completed on `r date()`. 

**Data Description:**

This dataset contains the daily count of rental bike transactions between years 2011 and 2012 in Capital bikeshare system with the corresponding weather and seasonal information.

**Data Source:** https://archive.ics.uci.edu/ml/datasets/bike+sharing+dataset

**Relevant Paper:** 

Fanaee-T, Hadi, and Gama, Joao. Event labeling combining ensemble detectors and background knowledge, Progress in Artificial Intelligence (2013): pp. 1-15, Springer Berlin Heidelberg



# Task One: Load and explore the data

## Load data and install packages

```{r}
# Import required packages
library(tidyverse)
library(lubridate)
library(timetk)
library(forecast)
library(tseries)

# Load the dataset
day_data <- read.csv("bike+sharing+dataset/day.csv")

# View the structure of the dataset
str(day_data)
summary(day_data)
glimpse(day_data)

# Checking for missing values
sum(is.na(day_data))

# Summary statistics
day_data %>%
  summarise(
    min_count = min(cnt),
    max_count = max(cnt),
    mean_count = mean(cnt),
    median_count = median(cnt)
  )
```


# Task Two: Create interactive time series plots

```{r}
# Convert date to date type
day_data$dteday <- as.Date(day_data$dteday)

# Interactive plot of daily bike rentals
day_data %>%
  plot_time_series(dteday, cnt, .interactive = TRUE, .title = "Daily Bike Rentals Over Time")
```


# Task Three: Smooth time series data

```{r}
# Apply moving average for smoothing
smoothed_cnt <- stats::filter(day_data$cnt, rep(1/7, 7), sides = 2)

# Plot the smoothed data
plot(day_data$dteday, smoothed_cnt, type = "l", col = "blue", main = "Smoothed Daily Bike Rentals", xlab = "Date", ylab = "Count")
```


# Task Four: Decompose and assess the stationarity of time series data

```{r}
# Convert to time series object
ts_data <- ts(day_data$cnt, frequency = 365)

# Decompose the time series
decomposed <- decompose(ts_data)
plot(decomposed)

# Augmented Dickey-Fuller Test for stationarity
adf.test(ts_data)
```


# Task Five: Fit and forecast time series data using ARIMA models

```{r}
# Fit ARIMA model
arima_model <- auto.arima(ts_data)
summary(arima_model)

# Forecast the next 30 days
forecast_values <- forecast(arima_model, h = 30)
plot(forecast_values)
```


# Task Six: Findings and Conclusions

```{r}
print("The dataset revealed seasonal patterns affecting bike rentals.")
print("Smoothing and decomposition techniques highlighted trends and seasonality.")
print("ARIMA model provided a baseline for forecasting future rental demand.")
print("Further model refinements can be applied for improved accuracy.")
