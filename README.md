# Predicting Denver Temperature

By Yu Jie Zhang, zhngyj@umich.edu

# Table of Contents
- [Introduction](#introduction)
- [Data Cleaning](#data-cleaning)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Problem Identification](#problem-identification)
- [Baseline Model](#baseline-model)
- [Final Model](#final-model)
- [Conclusion](#conclusion)

## Introduction
Accurate forecasts of daily average temperature are crucial for a wide range of applications -- from optimizing energy demand and crop management to planning outdoor events and ensuring public safety. In this project, I gather over six decades of historical meteorological data from Boulder, Colorado (1960–2024) to build a model that predicts each day’s average temperature for 2024 based solely on past temperature records and a handful of engineered features. By leveraging a pipeline of data cleaning, feature engineering, and linear modeling techniques, I aim to quantify how much of the variability in tomorrow’s temperatures can be explained by the climatological norm and recent trends.

This project investigates the question of **what is the average temperature of each day in 2024** and whether this is something we can predict using a linear model.

The dataset is collected by scraping the National Weather Service API and spans from 1960 to 2024 and contains charactaristics for each day of the year.
Data size: **23742 rows** and **7 columns**

## Features:
- **`avgt`**: Average temperature of the day in Fahrenheit
- **`min`**: Minimum temperature in Fahrenheit
- **`max`**: Maximum temperature in Fahrenheit
- **`pcpn`**: Measured precipitation in inches











