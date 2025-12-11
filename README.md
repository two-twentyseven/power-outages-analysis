# Power Outage Analyzanator
Power Outage Analyzanator is a detailed UCSD data science project that seeks to explore how data-driven insights may influence real-world decisions. This project features various comprehensive steps, from cleaning data and exploratory data analysis, to testing hypotheses and building a functioning predictive model. The overarching mission of this project is to pinpoint the characteristics of major power outages with higher severity as well as idenify the risk factors an energy company may consider looking into when predicting the location and severity of its next major power outage.

By: Jocelyn Ng & Holden Flett

# Introduction
## General Introduction
Throughout this project, we leveraged a dataset regarding major power outage events in the continental U.S. between January 2000 through July 2016. According to the Department of Energy, major outages are defined as “those that impacted at least 50,000 customers or caused an unplanned firm load loss of at least 300MW” [1]. In addition to the general major outage data, this dataset is characterized by geographical location, date and time, regional climate, land-use, electricity consumption patterns, and economic characteristics of the states impacted by the outages.

Our analysis is centered around one research question: **To what extent do regional climatic and economic characteristics impact the severity (duration) of a major power outage?** We use various data techniques to explore the imapct of climate anomalies, urbanization, and economic output on outage metrics. Our main focus will be on outage duration. Through analyzing these characteristics, we aim to develop a model that predicts the severity of future major outages. Attempting to fulfill our mission in enhancing data-driven decisions in the real-world, our predicitive model has the potential to optimize energy providers’ allocation of resources and emergency response.


## Introduction of Columns
The initial raw DataFrame consists of 1,534 observations and 57 variables, where each row represents a single power outage. For the sake of our analysis, we will be focusing on the columns listed below.

|           Column          |                                                  Description                                                  |
|:-------------------------:|:-------------------------------------------------------------------------------------------------------------:|
|     `OUTAGE.DURATION`     |                                     Duration of outage events (in minutes)                                    |
|      `DEMAND.LOSS.MW`     | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported] |
|    `CUSTOMERS.AFFECTED`   |  Number of customers affected by the power outage event|
|     `TOTAL.CUSTOMERS`     |  Annual number of total customers served in the U.S. state |
|        `U.S._STATE`       |  Represents all the states in the continental US.|
|       `NERC.REGION`       | The North American Electric Reliability Corporation (NERC) regions involved in the outage event |
|      `CLIMATE.REGION`     | U.S. Climate regions as specified by National Centers for Environmental Information (nine climatically consistent regions in continental U.S.A.)|
|           `YEAR`          |   Indicates the year when the outage event occurred|
|          `MONTH`          |     Indicates the month when the outage event occurred|
|      `ANOMALY.LEVEL`      |    Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season |
|     `CLIMATE.CATEGORY`    |  the climate episodes corresponding to the years (“Warm”, “Cold” or “Normal”)|
|      `CAUSE.CATEGORY`     |  Categories of all the events causing the major power outages|
|  `CAUSE.CATEGORY.DETAIL`  | Detailed description of the event categories causing the major power outages|
|       `TOTAL.PRICE`       |  Average monthly electricity price in the U.S. state (cents/kilowatt-hour)|
|        `RES.PRICE`        | Monthly electricity price in the residential sector (cents/kilowatt-hour)|
|        `COM.PRICE`        | Monthly electricity price in the commercial sector (cents/kilowatt-hour)|
|        `IND.PRICE`        |  Monthly electricity price in the industrial sector (cents/kilowatt-hour)|
|       `TOTAL.SALES`       | Total electricity consumption in the U.S. state (megawatt-hour)|
|        `RES.SALES`        | Electricity consumption in the residential sector (megawatt-hour)|
|        `COM.SALES`        | Electricity consumption in the commercial sector (megawatt-hour)|
|        `IND.SALES`        | Electricity consumption in the industrial sector (megawatt-hour)|
|     `PC.REALGSP.STATE`    | Per capita real gross state product (GSP) in the U.S. state (measured in 2009 chained U.S. dollars)|
|       `UTIL.CONTRI`       |  Utility industry׳s contribution to the total GSP in the State (expressed as percent of the total real GDP that is contributed by the Utility industry) (in %)|
|        `POPULATION`       |  Population in the U.S. state in a year|
|       `POPPCT_URBAN`      | Percentage of the total population of the U.S. state represented by the urban population (in %)|
|       `POPDEN_URBAN`      | Population density of the urban areas (persons per square mile)|
|        `AREAPCT_UC`       | Percentage of the land area of the U.S. state represented by the land area of the urban clusters (in %)|
|    `OUTAGE.START.DATE`    |  The day of the year when the outage event started (as reported by the corresponding Utility in the region)|
|    `OUTAGE.START.TIME`    |  The time of the day when the outage event started (as reported by the corresponding Utility in the region)|
| `OUTAGE.RESTORATION.DATE` | The day of the year when power was restored to all the customers (as reported by the corresponding Utility in the region)|
| `OUTAGE.RESTORATION.TIME` | The time of the day when power was restored to all the customers (as reported by the corresponding Utility in the region)|

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning

## Univariate Analysis

## Bivariate Analysis

## Interesting Aggregates

# Assessment of Missingness

## NMAR Analysis
We believe that the column `CUSTOMERS.AFFECTED`, which shows the number of customers affected by the power outage event, is likely Not Missing at Random (NMAR). NMAR means that the missing data is linked to the actual values that are missing. To determine whether this column is NMAR, we calculated the percentage of missing values and found that they occur most often in certain categories like “Fuel Supply Emergency,” “Public Appeal,” and “Intentional Attack.” If “threats” or “requests” do not cause any power loss for users, the actual number of users affected is 0. We think the data reporters may have left the field blank because they felt it was not applicable.

Another piece of data that would make the `CUSTOMERS.AFFECTED` column Missing at Random (MAR) is a variable that shows if a physical outage actually happened. We would use `1` to indicate power was disconnected for at least one customer and `0` to indicate the event was just an alert with no service loss. Having this information would change the reliance from the unknown value (the missing count of 0) to this new Yes/No variable, thus meeting the MAR definition.

## Missingness Dependency
We selected the `CUSTOMERS.AFFECTED` column to analyze the dependency of the missingess in comparison to the `TOTAL.PRICE` and `CAUSE.CATEGORY` columns.

In this section, we are going to observe the distribution of the Cause Category and Total Price when the Customers Affected column is missing versus not missing. We will do this through a permutation test with a significance level of 0.05 for both tests. The test statistic for the Cause category and Total Price permutation tests are Total Variation Distance and Absolute Difference of Means, respectively.  

### Cause Category 
**Null Hypothesis**: The distribution of `CAUSE.CATEGORY` is the same when `CUSTOMERS.AFFECTED` is missing vs not missing.

**Alternate hypothesis**: The distribution of `CAUSE.CATEGORY` is different when `CUSTOMERS.AFFECTED` are missing vs not missing.

<iframe
  src="assets/CCHist_plot.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

Through performing this permutation test, we noticed that the observed statistic is: 0.557 and the p-value is: 0.0. Since the p-value is less than the significance value we previously defined, we reject the null hypothesis, indicating that the distribution of the `CAUSE.CATEGORY` is different when `CUSTOMERS.AFFECTED` are missing vs not missing. We note that this seems to be a Missing at Random (MAR) situation. For example, the outages caused by "severe weather" might be reported differently than those caused by "intentional attacks."

Shown below is the empirical distribution of the TVDs.

<iframe
  src="assets/CCEmpDis_plot.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

### Total Price
**Null Hypothesis**: The distribution of `TOTAL.PRICE` is the same when `CUSTOMERS.AFFECTED` is missing vs not missing.

**Alternate hypothesis**: The distribution of `TOTAL.PRICE` is different when `CUSTOMERS.AFFECTED` are missing vs not missing.

<iframe
  src="assets/TPBoxplot_plot.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

Through performing this permutation test, we noticed that the observed statistic is: 0.118 and the p-value is: 0.4778. Since the p-value is much greater than the significance value we previously defined, we fail to reject the null hypothesis, indicating that the distribution of the `TOTAL.PRICE` is the same when `CUSTOMERS.AFFECTED` are missing vs not missing. We note that this seems to be a Missing Completely at Random (MCAR) situation. Specifically, the likelihood of an outage report missing from the `CUSTOMERS.AFFECTED` column is consistent whether an outage occurs at various energy prices.

Shown below is the empirical distribution of the Absolute Difference of Means.

<iframe
  src="assets/TPEmpDis_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis

# References
[1] S. Mukherjee, R. Nateghi, and M. Hastak, “Data on major power outage events in the continental U.S.,” Data in Brief, vol. 19, pp. 2079–2083, 2018, doi: 10.1016/j.dib.2018.06.067.
