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
 width="1000"
 height="600"
 frameborder="0"
 ></iframe>

This violin plot shows the distribution of average temperatures across months in all years. Based on the plot we can clearly see that Denver in general follows the "normal" temperature of being hotter in the summer and colder in the winter. We can also observe that in general, the summer months have a smaller range of average temperatures while the deviation is larger in the winter.

 <iframe
 src="assets/daily_avg_temp.html"
 width="1000"
 height="600"
 frameborder="0"
 ></iframe>

This plot shows the seasonality from 2014 to 2024 highlighting the fact that Denver hasn't experienced a lot of changes in terms of drastic temperature increases or decreases. There are of course outlier days that are warmer or colder than the usual which are highlighted by the spikes

**<u>Bivariate Analysis</u>**

 <iframe
 src="assets/min_max.html"
 width="1000"
 height="600"
 frameborder="0"
 ></iframe>

This plot shows that, in general, the higher the minimum temperature, the higher the daytime high temperature -- i.e. warmer nights almost always lead to warmer days. This tight, roughly linear band of points shows a strong positive correlation between the daily minimum and maximum temperatures


 <iframe
 src="assets/pcpn_vs_temp.html"
 width="1000"
 height="600"
 frameborder="0"
 ></iframe>

From this plot we can deduce that days with average temperatures well below freezing see the most precipitation (likely as snow), and mean daily precipitation then steadily falls off as it warms, reaching its lowest values on the hottest days.

**<u>Aggregate Analysis</u>**

| month_abbr   |   1954-1964 |   1964-1974 |   1974-1984 |   1984-1994 |   1994-2004 |   2004-2014 |   2014-2024 |
|:-------------|------------:|------------:|------------:|------------:|------------:|------------:|------------:|
| Jan          |        30.5 |        33.5 |        30.8 |        33.3 |        34.6 |        34.6 |        34   |
| Feb          |        35.1 |        36.1 |        36.4 |        34.4 |        36.2 |        34   |        34.8 |
| Mar          |        38.9 |        41   |        40.5 |        43.1 |        42.3 |        43.7 |        42.4 |
| Apr          |        50.7 |        48.4 |        48.4 |        50.3 |        48.4 |        49.1 |        48.9 |
| May          |        60.7 |        58.3 |        56.6 |        58.4 |        57.4 |        57.5 |        55.8 |
| Jun          |        68.3 |        66.7 |        66.8 |        67.4 |        65.5 |        67.8 |        68.3 |
| Jul          |        74.7 |        73   |        73   |        71.1 |        73.1 |        73.4 |        73.1 |
| Aug          |        72.4 |        71.3 |        70.7 |        70.1 |        70.8 |        71.6 |        72   |
| Sep          |        64   |        61.5 |        63.5 |        62.1 |        62.6 |        63.8 |        66.3 |
| Oct          |        56.1 |        52.3 |        52.1 |        52.2 |        51.8 |        51.7 |        52.9 |
| Nov          |        42.7 |        40.6 |        40.8 |        38.8 |        40.9 |        43.2 |        42.8 |
| Dec          |        34.6 |        33.4 |        35.9 |        32.7 |        35.1 |        32.5 |        35.8 |

This pivot table of mean daily average temperatures for each month-decade combination, order the rows from January through December and the columns chronologically, and round all values to one decimal place. The resulting chart makes it easy to see that, while some months' temperature increased steadily others remained the same across all decades.








