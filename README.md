# Predicting Denver Temperature

By Yu Jie Zhang, zhngyj@umich.edu

# Table of Contents
- [Introduction](#introduction)
- [Data Cleaning and Exploratory Data Analysis](#exploratory-data-analysis)
- [Problem Identification](#problem-identification)
- [Baseline Model](#baseline-model)
- [Final Model](#final-model)
- [Conclusion](#conclusion)

## Introduction
Accurate forecasts of daily average temperature are crucial for a wide range of applications -- from optimizing energy demand and crop management to planning outdoor events and ensuring public safety. In this project, I gather over six decades of historical meteorological data from Boulder, Colorado (1960–2024) to build a model that predicts each day’s average temperature for 2024 based solely on past temperature records and a handful of engineered features. By leveraging a pipeline of data cleaning, feature engineering, and linear modeling techniques, I aim to quantify how much of the variability in tomorrow’s temperatures can be explained by the climatological norm and recent trends.

This project investigates the question of **what is the average temperature of each day in 2024** and whether this is something we can predict using a linear model.

The dataset is collected by scraping the National Weather Service API and spans from 1960 to 2024 and contains charactaristics for each day of the year.
Data size: **23742 rows** and **7 columns**

### Features:
- **`avgt`**: Average temperature of the day in Fahrenheit
- **`min`**: Minimum temperature in Fahrenheit
- **`max`**: Maximum temperature in Fahrenheit
- **`pcpn`**: Measured precipitation in inches

## Data Cleaning and Exploratory Data Analysis
To ensure a meaningful and valid data analysis, the following steps were taken to clean and organize the dataset:

**Removing unnecessary columns**
Since February 29. happens once in four years, I decided to drop all of the data that had date of February 29.

**Imputing missing values** 
Despite dropping Feb. 29 data I still had **5208** values missing, that span all features with the most data missing from precipitation. Since this was a large amount of the dataset and since **`avgt`** was relatively intact, I decided to impute missing data using mean imputation.

**Cleaned dataset**
After cleaning this is how my dataset looks like:

|    | date                |   max |   min |   avgt |   pcpn | md    |
|---:|:--------------------|------:|------:|-------:|-------:|:------|
|  0 | 1960-01-01 00:00:00 |    31 |    15 |   23   |   0.07 | 01-01 |
|  1 | 1960-01-02 00:00:00 |    32 |     4 |   18   |   0    | 01-02 |
|  2 | 1960-01-03 00:00:00 |    24 |     5 |   14.5 |   0    | 01-03 |
|  3 | 1960-01-04 00:00:00 |    30 |     3 |   16.5 |   0    | 01-04 |
|  4 | 1960-01-05 00:00:00 |    36 |    10 |   23   |   0    | 01-05 |

**<u>Univariate Analysis</u>**
 <iframe
 src="assets/violin_plot.html"
 width="1200"
 height="600"
 frameborder="0"
 ></iframe>

This violin plot shows the distribution of average temperatures across months in all years. Based on the plot we can clearly see that Denver in general follows the "normal" temperature of being hotter in the summer and colder in the winter. We can also observe that in general, the summer months have a smaller range of average temperatures while the deviation is larger in the winter.

 <iframe
 src="assets/daily_avg_temp.html"
 width="1200"
 height="600"
 frameborder="0"
 ></iframe>

This plot shows the seasonality from 2014 to 2024 highlighting the fact that Denver hasn't experienced a lot of changes in terms of drastic temperature increases or decreases. There are of course outlier days that are warmer or colder than the usual which are highlighted by the spikes

**<u>Bivariate Analysis</u>**

 <iframe
 src="assets/min_max.html"
 width="1200"
 height="600"
 frameborder="0"
 ></iframe>

In this plot we can see that, in general, the higher the minimum temperature, the higher the daytime high temperature -- i.e. warmer nights almost always lead to warmer days. This tight, roughly linear band of points shows a strong positive correlation between the daily minimum and maximum temperatures

