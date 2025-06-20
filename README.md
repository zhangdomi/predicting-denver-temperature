**Predicting Denver's Temperature** \
By Yu Jie Zhang, zhngyj@umich.edu

# Table of Contents
- [Introduction](#introduction)
- [Data Cleaning and Exploratory Data Analysis](#data-cleaning-and-exploratory-data-analysis)
- [Framing a Prediction Problem](#framing-a-prediction-problem)
- [Baseline Model](#baseline-model)
- [Final Model](#final-model)
- [Conclusion](#conclusion)

## Introduction
Ever wondered what the temperature is going to be in Denver tomorrow and didn't want to open your weather app to check it? Or perhaps you are an active user of Kalshi and wanted to make more accurate predictions? Well, this project might serve as a guideline for both as I'm trying to explore the temperature of Denver. \
Accurate forecasts of daily average temperature are crucial for a wide range of applications -- from optimizing energy demand and crop management to planning outdoor events and ensuring public safety and (of course) making accurate bets on Kalshi. In this project, I gather over six decades of historical meteorological data from Boulder, Colorado (1960–2024) to build a model that predicts each day’s average temperature for 2024 based solely on past temperature records and a handful of engineered features. By leveraging a pipeline of data cleaning, feature engineering, and linear modeling techniques, I aim to quantify how much of the variability in tomorrow’s temperatures can be explained by the climatological norm and recent trends.

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

## Framing a Prediction Problem

The ultimate goal is to predict the average temperature **`avgt`** of Denver in a given day using previous years' of data, this is a regression problem and hence I will use **`sklearn`**'s `linear_model` library. Predicting the average temperature will give me a general idea of how a certain day's temperature market might look like on Kalshi.

This project is unique in that the data is comprised of time series data and that the features used for the prediction are the previous years of the same day. It is necessary to acknowledge that although this seems to make sense on surface level, it is not the ideal way of predicting as the prediction might not be able to capture some relevant information. For example, other predictive models might use a rolling window of last 7 days that might capture the general trend of a certain year better. 

The model will be evaluated using three metrics:

* **`Mean Absolute Error (MAE)`**: gives the average prediction error in °F and is robust to outliers.

* **`Mean Squared Error (MSE)`**: penalizes larger errors more heavily, ensuring that days with unexpectedly large misses are driven down.

* **`Root Mean Squared Error (RMSE)`**: the square root of MSE, which brings the penalization of large errors back into the original °F units for easy interpretation.


## Baseline Model

The initial model is a multiple linear regression with the following features:

**Response:**

* **avgt_2024** (Quantitative)

**Predictors:**

* **avgt_1960, avgt_1961, …, avgt_2023** (All quantitative historical daily average temperatures)

Because all predictors are already numeric, no categorical encoding was necessary. The baseline was implemented using an **`sklearn`** Pipeline composed of a **`StandardScaler`** (to center and scale each feature) followed by a **`LinearRegression`** estimator. A 75–25 train–test split (`random_state=42`) was used to evaluate performance.


**Test Performance**:

- **MAE: 5.44 °F**
- **MSE: 55.32 °F<sup>2</sup>**
- **RMSE: 7.44 °F**

An average test‐set error of 7.4 °F and average absolute error of 5.44 °F indicates that while the model learns the broad seasonal pattern, its day‐to‐day predictions remain off by a week’s temperature swing on average. This suggests room for improvement via feature engineering (e.g. rolling windows, seasonality cycles) and regularization.

Furthermore, upon inspection I found that the model coefficients are not around 1/64 same as one would expect, i.e. averaging out all the year, rather it's a mix of positive and negative values that range from -4 to 4 usually. This means the linear regression model is emphasizing some years while viewing others are not as important. 

## Final Model

To improve on the baseline model, I made a few enhancements:
**Feature Engineering**
* **Historical variability (volatility)**: Added **`avgt_std`**, the standard deviation of the daily average temperature across all prior years, to capture overall inter-annual variability.
* **Recent extremes**: Kept only the minimum and maximum temperatures from the two most recent years (`min_2022`, `max_2022`, `min_2023`, `max_2023`) to focus on recent extreme conditions and reduce dimensionality.
* **Seasonality**: Created two cyclical features, `sin_doy` and `cos_doy`, by mapping each calendar day (“MM-DD”) to its day-of-year and applying sine/cosine transforms to model the annual temperature cycle.

**Pipeline & Features**

* Quantitative features (71 total)

    * 64 historical averages: `avgt_1960` through `avgt_2023`

    * 4 recent extremes: `min_2022`, `max_2022`, `min_2023`, `max_2023`
    
    * 1 variability measure: `avgt_std`

    * 2 cyclical seasonality terms: `sin_doy`, `cos_doy`

* Encodings: All features are numeric, so no categorical or ordinal encoding was required.

* Model:
    * `FunctionTransformer`s for feature engineering

    * `StandardScaler` to center and scale

    * **`Lasso regression`** with `alpha=0.1` (chosen by 7-fold CV over a logarithmic grid)

**Final Model Performance**:

- **MAE: 5.01 °F**
- **MSE: 46.80 °F<sup>2</sup>**
- **RMSE: 6.84 °F**

The final model achieves a **0.6 °F improvement on RMSE** and approximately **0.5 °F improvement on MAE**, confirming that the engineered features and L1 regularization meaningfully improve day-ahead average temperature predictions. However, we have to keep in mind that while the errors decreased, an average prediction error of 5-6 Fahrenheit is still significant. 

## Conclusion
Finally, while this project has achieved its goal of improving model error, I believe that the initial approach of using a linear model trained on the days' of previous year might not have been the best approach. While we can expect some kind of relationship between days' of previous years there's likely a much better relationship to discover by using a model that uses previous days' data up to the day of prediction. 
Going forward, I might change the model to something more complex but that's up to discussion. Furthermore, predicting the extremas (max and min) also becomes harder as linear models aren't able to capture the intricacies.










 





