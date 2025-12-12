# Power Outage Analyzanator
Power Outage Analyzanator is a detailed UCSD data science project that seeks to explore how data-driven insights may influence real-world decisions. This project features various comprehensive steps, from cleaning data and exploratory data analysis, to testing hypotheses and building a functioning predictive model. The overarching mission of this project is to pinpoint the characteristics of major power outages with higher severity as well as idenify the risk factors an energy company may consider looking into when predicting the location and severity of its next major power outage.

By: Jocelyn Ng & Holden Flett

# Introduction
## General Introduction
Throughout this project, we leveraged a dataset regarding major power outage events in the continental U.S. between January 2000 through July 2016. According to the Department of Energy, major outages are defined as “those that impacted at least 50,000 customers or caused an unplanned firm load loss of at least 300MW” [1]. In addition to the general major outage data, this dataset is characterized by geographical location, date and time, regional climate, land-use, electricity consumption patterns, and economic characteristics of the states impacted by the outages.

Our analysis is centered around one research question: **To what extent do certain characteristics impact the severity (duration) of a major power outage?** We use various data techniques to explore the imapct of climate anomalies, urbanization, and economic output on outage metrics. Our main focus will be on outage duration. Through analyzing these characteristics, we aim to develop a model that predicts the severity of future major outages. Attempting to fulfill our mission in enhancing data-driven decisions in the real-world, our predictive model has the potential to optimize energy providers’ allocation of resources and emergency response.


## Introduction of Columns
The initial raw DataFrame consists of 1,534 observations and 57 variables, where each row represents a single power outage. For the sake of our analysis, we will be focusing on these most commonly used columns listed below.

|           Column          |                                                  Description                                                  |
|:-------------------------:|:-------------------------------------------------------------------------------------------------------------:|
|     `OUTAGE.DURATION`     |                                     Duration of outage events (in minutes)                                    |
|      `DEMAND.LOSS.MW`     | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported] |
|    `CUSTOMERS.AFFECTED`   |  Number of customers affected by the power outage event|
|        `U.S._STATE`       |  Represents all the states in the continental US.|
|       `NERC.REGION`       | The North American Electric Reliability Corporation (NERC) regions involved in the outage event |
|      `CLIMATE.REGION`     | U.S. Climate regions as specified by National Centers for Environmental Information (nine climatically consistent regions in continental U.S.A.)|
|           `YEAR`          |   Indicates the year when the outage event occurred|
|          `MONTH`          |     Indicates the month when the outage event occurred|
|      `ANOMALY.LEVEL`      |    Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season |
|     `CLIMATE.CATEGORY`    |  the climate episodes corresponding to the years (“Warm”, “Cold” or “Normal”)|
|      `CAUSE.CATEGORY`     |  Categories of all the events causing the major power outages|
|        `RES.CUSTOMERS`        | Annual number of customers served in the residential electricity sector of the U.S. state |
|        `COM.CUSTOMERS`        | Annual number of customers served in the commercial electricity sector of the U.S. state |
|        `IND.CUSTOMERS`        | Annual number of customers served in the industrial electricity sector of the U.S. state |
|    `DUR.LOG`    |  The logged duration of outage events (in minutes)  |

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning
1) Since we used most of our columns in our analysis, the only columns we needed to drop were "variables" and "POSTAL.CODE". The former being a NaN column while the latter being redundant for the U.S. state column. 

2) We assigned OBS, which is the observation unique identifier, to the index. 

3) We also converted the start and end times to timestamp objects, but did not end up using them in our analysis. 

4) During our regression, we scanned all non-timestamp columns for a valid R2 coefficients while fully excluding missing values during the regression. 

5) Aside from the variables column, we did not explicitly remove missing values, due to the specificity of how we handled them depending on the later steps (such as our MAR and NMAR analyses). 

6) Additionally, we added `DUR.LOG` which is the natural log of our durations. This is because the distribution of our outage durations is right skewed and logging it normalizes the column, making it easier to eventually predict.

The first few rows of this cleaned `outages` DataFrame are shown below.

|   YEAR |   MONTH | U.S._STATE   | NERC.REGION   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   HURRICANE.NAMES |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   RES.PRICE |   COM.PRICE |   IND.PRICE |   TOTAL.PRICE |   RES.SALES |   COM.SALES |   IND.SALES |   TOTAL.SALES |   RES.PERCEN |   COM.PERCEN |   IND.PERCEN |   RES.CUSTOMERS |   COM.CUSTOMERS |   IND.CUSTOMERS |   TOTAL.CUSTOMERS |   RES.CUST.PCT |   COM.CUST.PCT |   IND.CUST.PCT |   PC.REALGSP.STATE |   PC.REALGSP.USA |   PC.REALGSP.REL |   PC.REALGSP.CHANGE |   UTIL.REALGSP |   TOTAL.REALGSP |   UTIL.CONTRI |   PI.UTIL.OFUSA |   POPULATION |   POPPCT_URBAN |   POPPCT_UC |   POPDEN_URBAN |   POPDEN_UC |   POPDEN_RURAL |   AREAPCT_URBAN |   AREAPCT_UC |   PCT_LAND |   PCT_WATER_TOT |   PCT_WATER_INLAND |   DUR.LOG | OUTAGE.START        | OUTAGE.RESTORATION   |
|-------:|--------:|:-------------|:--------------|:-------------------|----------------:|:-------------------|:-------------------|:------------------------|------------------:|------------------:|-----------------:|---------------------:|------------:|------------:|------------:|--------------:|------------:|------------:|------------:|--------------:|-------------:|-------------:|-------------:|----------------:|----------------:|----------------:|------------------:|---------------:|---------------:|---------------:|-------------------:|-----------------:|-----------------:|--------------------:|---------------:|----------------:|--------------:|----------------:|-------------:|---------------:|------------:|---------------:|------------:|---------------:|----------------:|-------------:|-----------:|----------------:|-------------------:|----------:|:--------------------|:---------------------|
|   2011 |       7 | Minnesota    | MRO           | East North Central |            -0.3 | normal             | severe weather     | nan                     |               nan |              3060 |              nan |                70000 |       11.6  |        9.18 |        6.81 |          9.28 | 2.33292e+06 | 2.11477e+06 | 2.11329e+06 |   6.56252e+06 |      35.5491 |      32.225  |      32.2024 |         2308736 |          276286 |           10673 |           2595696 |        88.9448 |        10.644  |       0.411181 |              51268 |            47586 |          1.07738 |                 1.6 |           4802 |          274182 |       1.75139 |             2.2 |      5348119 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |  8.0265   | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |
|   2014 |       5 | Minnesota    | MRO           | East North Central |            -0.1 | normal             | intentional attack | vandalism               |               nan |                 1 |              nan |                  nan |       12.12 |        9.71 |        6.49 |          9.28 | 1.58699e+06 | 1.80776e+06 | 1.88793e+06 |   5.28423e+06 |      30.0325 |      34.2104 |      35.7276 |         2345860 |          284978 |            9898 |           2640737 |        88.8335 |        10.7916 |       0.37482  |              53499 |            49091 |          1.08979 |                 1.9 |           5226 |          291955 |       1.79    |             2.2 |      5457125 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |  0.693147 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |
|   2010 |      10 | Minnesota    | MRO           | East North Central |            -1.5 | cold               | severe weather     | heavy wind              |               nan |              3000 |              nan |                70000 |       10.87 |        8.19 |        6.07 |          8.15 | 1.46729e+06 | 1.80168e+06 | 1.9513e+06  |   5.22212e+06 |      28.0977 |      34.501  |      37.366  |         2300291 |          276463 |           10150 |           2586905 |        88.9206 |        10.687  |       0.392361 |              50447 |            47287 |          1.06683 |                 2.7 |           4571 |          267895 |       1.70627 |             2.1 |      5310903 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |  8.0067   | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |
|   2012 |       6 | Minnesota    | MRO           | East North Central |            -0.1 | normal             | severe weather     | thunderstorm            |               nan |              2550 |              nan |                68200 |       11.79 |        9.25 |        6.71 |          9.19 | 1.85152e+06 | 1.94117e+06 | 1.99303e+06 |   5.78706e+06 |      31.9941 |      33.5433 |      34.4393 |         2317336 |          278466 |           11010 |           2606813 |        88.8954 |        10.6822 |       0.422355 |              51598 |            48156 |          1.07148 |                 0.6 |           5364 |          277627 |       1.93209 |             2.2 |      5380443 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |  7.84424  | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |
|   2015 |       7 | Minnesota    | MRO           | East North Central |             1.2 | warm               | severe weather     | nan                     |               nan |              1740 |              250 |               250000 |       13.07 |       10.16 |        7.74 |         10.43 | 2.02888e+06 | 2.16161e+06 | 1.77794e+06 |   5.97034e+06 |      33.9826 |      36.2059 |      29.7795 |         2374674 |          289044 |            9812 |           2673531 |        88.8216 |        10.8113 |       0.367005 |              54431 |            49844 |          1.09203 |                 1.7 |           4873 |          292023 |       1.6687  |             2.2 |      5489594 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |  7.46221  | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |


## Univariate Analysis
As a part of our Exploratory Data Analysis, we first perform a univariate analysis examining the distribution of three single variables.

We are looking to reveal patterns that are hidden by extreme values, so we opted for a natural log distribution of the Power Outage Durations.
<iframe
  src="assets/NLogDist.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>
The distribution of outage durations on a logarithmic scale shows two clear peaks. This chart displays a main cluster of long-duration events that is roughly normal, centered around a log value of 8, which is about 3,000 minutes. There is also a smaller peak of very short outages near 0. This suggests that major power outages usually fit into two categories: 1) complex infrastructure repairs where most are resolved quickly, but a few unfortunate ones take much longer, and 2) a separate group of brief but significant incidents.

<iframe
  src="assets/cause_category_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
This bar chart shows how often power outages happen by cause. Severe weather stands out as the main reason, with nearly 800 incidents, which is almost double the number for the second highest category, intentional attacks. The data shows a sharp downward trend indicating  that while environmental conditions are the main cause of outages, other operational and technical issues happen much less often.

<iframe
  src="assets/climate_region_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
This bar chart shows how often power outages happen in different climate regions. We can observe that the Northeast has the highest number of outages, around 350. The data indicates a clear drop in outage numbers from the busy Northeast and South regions down to the West North Central region, which reports the fewest outages. This trend suggests that the weather patterns and atmospheric conditions in the Northeast and South regions are much more likely to cause power disruptions than those in the West North Central or Southwest.

## Bivariate Analysis
We aim to observe how the value of one variable changes in relation to the other, in order to better assess their impact on outage severity. 
In this bivariate analysis, we observed the relationship between both outage duration and customers affected statistics, as well as the frequency of year versus month.

<iframe
  src="assets/outage_scatterplot.html"
  width="800"
  height="470"
  frameborder="0"
></iframe>
The outage duration versus customers affected scatter plot shows a large cluster near the origin with no strong correlation. This suggests that most outages are brief and affect a small number of customers. Unexpectedly, the trend in the outliers, highlights that the longest outages usually impact fewer people, while widespread outages affecting millions tend to be shorter.
<br>

<iframe
  src="assets/UniHeatmap.html"
  width="800"
  height="600"
  frameborder="0"
  style="display: block; margin: 0 auto;"
></iframe>
The heatmap shows the frequency of power outages throughout the seasons and years. It clearly shows more outages occurring in the summer months from June to August. This might be due to higher energy demand or seasonal weather. It also highlights a significant spike in outages around 2011, marking it as the most active period in the data compared to the quieter early 2000s.

## Interesting Aggregates

### Cause Category Statistics

| CAUSE.CATEGORY                |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED |   Count |
|:------------------------------|------------------:|---------------------:|--------:|
| equipment failure             |          1816.91  |        101936        |      60 |
| fuel supply emergency         |         13484     |             0.142857 |      51 |
| intentional attack            |           429.98  |          1790.53     |     418 |
| islanding                     |           200.545 |          6169.09     |      46 |
| public appeal                 |          1468.45  |          7618.76     |      69 |
| severe weather                |          3883.99  |        188575        |     763 |
| system operability disruption |           728.87  |        211066        |     127 |

We grouped the data by `CAUSE.CATEGORY` and calculated the frequency (count) and mean severity metrics. By comparing how often an outage occurs with its actual impact, we distinguished between high-frequency risks and high-severity risks. For example, Severe Weather has the highest frequency, occurring most often. However, Fuel Supply Emergencies and System Operability Disruptions show greater impact in terms of duration and customer reach. We gathered that the most common risks are not always the ones that consume the most resources.

### Climate Region vs. Cause Category (Mean Outage Duration)

| CLIMATE.REGION     |   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
|:-------------------|--------------------:|------------------------:|---------------------:|------------:|----------------:|-----------------:|--------------------------------:|
| Central            |             322     |                 10035.2 |              346.059 |    125.333  |         1410    |          3250.01 |                        2695.2   |
| East North Central |           26435.3   |                 33971.2 |             2376.05  |      1      |          733    |          4434.82 |                        2610     |
| Northeast          |             215.8   |                 14629.6 |              195.985 |    881      |         2655    |          4429.9  |                         773.5   |
| Northwest          |             702     |                     1   |              373.812 |     73.3333 |          898    |          4838    |                         141     |
| South              |             295.778 |                 17482.5 |              325.607 |    493.5    |         1163.98 |          4391.35 |                         866.074 |
| Southeast          |             554.5   |                   nan   |              504.667 |    nan      |         2865.4  |          2662.56 |                         169.312 |
| Southwest          |             113.8   |                    76   |              265.672 |      2      |         2275    |         11572.9  |                         329.222 |
| West               |             524.81  |                  6154.6 |              857.677 |    214.857  |         2028.11 |          2928.37 |                         363.667 |
| West North Central |              61     |                   nan   |               23.5   |     68.2    |          439.5  |          2442.5  |                         nan     |

This pivot table uses `CLIMATE.REGION` and `CAUSE.CATEGORY` to look at the average outage duration in different environmental contexts. By comparing how specific causes occur in various climates, we found that severity often results from a mix of factors rather than being a single event. The Southwest region, when paired with Severe Weather, shows much higher "severe" statistics (averaging over 11,000 minutes) compared to the same cause in the Central region. This suggests that our model may need to consider both location and cause to effectively predict duration.

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
We decided to highlight the two regions that share the greatest power outage duration difference with a p value < 0.05. The columns of importance are ...

**Null Hypothesis:** On average, East North Central and Southwest equal median durations and power outages do not vary with respect to those regions.

**Alternative Hypothesis:** On average, power outages East North Central are longer than those from the Southwest.

Originally, this was 'East North Central' and 'West North Central'
Once we took a look at the value counts list of the regions, we discovered that 'East North Central' region only had 15 power outages in the cleaned dataset

Beyond that, all of East North Central's pairs had the greatest differences among pairs. Wary of drawing conclusions from minimal data, we elected to avoid using those pairs.

# Framing a Prediction Problem
Since our dataset is a set of descriptive qualities of power outages as well as their impact, we thought it best to predict the impact of a given outage from the circumstances around it. For impact, we decided to target duration, since it is a useful quality that, when predicted, can inform victims on how long to expect an outage to last. The problem then becomes, what's the best prediction model for the duration of an outage?

# Baseline Model
Our baseline regression model predicts outage duration using all of the `outages` dataframe features. We evaluate with train/test split and 5-fold cross-validation. This model performs poorly, capturing very little variance in the target. Our metrics include: Train R^2=0.256, Test R^2=0.017, Train MSE≈27,836,705, Test MSE≈26,807,090, CV Mean R^2=0.147

# Final Model
To improve the model, we switched the target to the log of outage duration.
We systematically dropped individual columns to assess their contribution to R^2.
We identified that losing the cause category caused the biggest drop in R^2, indicating it was the most significant.
The rest did not appear nearly as significant which prompted us to look for further explanatory columns

We then went through and systematically computed correlation R^2 values for every single column in the dataframe
to find other features to add to the model to predict DUR.LOG

After analyzing each column systematically for correlation with DUR.LOG, U.S. state was the only other feature
with an R^2 above 0.2 (about 0.22). This combined model with CAUSE.CATEGORY and U.S._STATE gives:
Train R^2: 0.507, Test R^2: 0.426, CV Mean R^2: 0.453

This begs the question, did we even need U.S. state? As it turns out the answer was no!
Simply using cause-category as a predictor was the strongest predictor of the logged duration
This model is less likely to overfit and turned out to be the strongest; the simplest model is the best.
Train R^2: 0.4416923618233445 Train MSE: 4.09747935754667


# Fairness Analysis
Permutation test to compare model performance across two NERC regions (RFC vs WECC)
We predict DUR.LOG using cause category and compute MSE separately for RFC and WECC
Null hypothesis: MSE difference between RFC and WECC is due to chance
Red line on histogram marks observed MSE difference

# References
[1] S. Mukherjee, R. Nateghi, and M. Hastak, “Data on major power outage events in the continental U.S.,” Data in Brief, vol. 19, pp. 2079–2083, 2018, doi: 10.1016/j.dib.2018.06.067.
