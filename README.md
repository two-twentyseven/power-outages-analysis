# Power Outage Analyzanator
Power Outage Analyzanator is a detailed UCSD data science project made to serve as documentation for a Power Outage Analysis tool.

By: Jocelyn Ng & Holden Flett

# Introduction
## General Introduction
Power Outage Analyzanator is a detailed UCSD data science project made to serve as documentation for a Power Outage Analysis tool. The project features comprehensive steps from cleaning data and exploratory data analysis, to testing hypotheses and building a functioning predictive model.

The overarching mission of this project is to pinpoint the characteristics of major power outages with higher severity as well as idenify the risk factors an energy company may consider looking into when predicting the location and severity of its next major power outage.

## Introduction of Columns
The initial raw DataFrame consists of 1,534 observations and 57 variables, where each row represents a single power outage.

| Column | Description |
| ----------- | ----------- |
| `OUTAGE.DURATION` | Duration of outage events (in minutes) |
| `DEMAND.LOSS.MW` | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported] |
# Data Cleaning and Exploratory Data Analysis

## Data Cleaning

## Univariate Analysis

## Bivariate Analysis

## Interesting Aggregates

# Assessment of Missingness

## NMAR Analysis

## Missingness Dependency
We selected the `CUSTOMERS.AFFECTED` column to analyze the dependency of the missingess in comparison to the `TOTAL.PRICE` and `CAUSE.CATEGORY` columns.

In this section, we are going to observe the distribution of the Cause Category and Total Price when the Customers Affected column is missing versus not missing. We will do this through a permutation test with a significance level of 0.05 for both tests. The test statistic for the Cause category and Total Price permutation tests are Total Variation Distance and Absolute Difference of Means, respectively.  

### Cause Category 
**Null Hypothesis**: The distribution of `CAUSE.CATEGORY` is the same when `CUSTOMERS.AFFECTED` is missing vs not missing.

**Alternate hypothesis**: The distribution of `CAUSE.CATEGORY` is different when `CUSTOMERS.AFFECTED` are missing vs not missing.

[Insert plots]

Through performing the permutation test, we noticed that the observed statistic is: 0.557 and the p-value is: 0.0. Since the p-value is less than the significance value we previously defined, we reject the null hypothesis, indicating that the distribution of the `CAUSE.CATEGORY` is different when `CUSTOMERS.AFFECTED` are missing vs not missing. We note that this seems to be a Missing at Random (MAR) situation. For example, the outages caused by "severe weather" might be reported differently than those caused by "intentional attacks."

Shown below is the empirical distribution of the TVDs.

[Insert Plot]

### Total Price
**Null Hypothesis**: The distribution of `TOTAL.PRICE` is the same when `CUSTOMERS.AFFECTED` is missing vs not missing.

**Alternate hypothesis**: The distribution of `TOTAL.PRICE` is different when `CUSTOMERS.AFFECTED` are missing vs not missing.

[Insert Plot]

Through performing this permutation test, we noticed that the observed statistic is: 0.118 and the p-value is: 0.4778. Since the p-value is much greater than the significance value we previously defined, we fail to reject the null hypothesis, indicating that the distribution of the `TOTAL.PRICE` is the same when `CUSTOMERS.AFFECTED` are missing vs not missing. We note that this seems to be a Missing Completely at Random (MCAR) situation. Specifically, the likelihood of an outage report missing from the `CUSTOMERS.AFFECTED` column is consistent whether an outage occurs at various energy prices.

Shown below is the empirical distribution of the Absolute Difference of Means.

[Insert Plot]

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis

