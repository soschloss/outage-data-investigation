# Introduction
This project is focused on a dataset containing information about power outages recorded in the U.S. from 2000 to 2016. This dataset contains a large amount of additional information, including information about the location, cause, and climate for each outage. This dataset contains data for 1534 outages, with each outage corresponding with one row. 

While investigating and analyzing this data, I will be focused on answering the question, **"What characteristics are associated with each category of cause?"**. By analyzing the causes of previously-recorded power outages and looking for correlations with other variables, we can gain knowledge that will help prevent outages or minimize outage severity in the future. 

Originally, this dataset had 57 columns, but I will be using a subset of the original columns relevant to my analysis:

| Column name                 | Description                                                                                                                          |
|-----------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| `'YEAR'`                    | The year each outage occurred                                                                                                        |
| `'MONTH'`                   | The month each outage event occurred                                                                                                 |
| `'U.S._STATE'`              | The state where each outage occurred                                                                                                 |
| `'NERC.REGION'`             | The North American Electric Reliability Corporation<br/>(NERC) involved in each outage                                               |
| `'CLIMATE.REGION'`          | The U.S. Climate Region (specified by National Centers<br/>for Environmental Information) corresponding with each contintental state |
| `'ANOMALY.LEVEL'`           | The oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season                                            |
| `'CLIMATE.CATEGORY'`        | The climate episodes for each year based on the ONI index                                                                            |
| `'OUTAGE.START.DATE'`       | The reported date when each outage started                                                                      |
| `'OUTAGE.START.TIME'`       | The reported time when each outage started                                                                                           |
| `'OUTAGE.RESTORATION.DATE'` | The reported date when each outage ended for all affected customers                                                                  |
| `'OUTAGE.RESTORATION.DATE'` | The reported time when each outage ended for all affected customers                                                                  |
| `'CAUSE.CATEGORY'`          | The category of events causing each outage                                                                                           |
| `'HURRICANE.NAMES'`         | The name of the hurricane for each outage caused by a hurricane                                                                      |
| `'OUTAGE.DURATION'`         | The duration of each outage in minutes                                                                                               |
| `'CUSTOMERS.AFFECTED'`      | The total number of customers affected by each outage                                                                                |
| `'TOTAL.PRICE'`             | The average monthly electricity price in each state (cents/kilowatt-hour)                                                            |
| `'TOTAL.SALES'`             | The total electricity consumption in each state (megawatt-hour)                                                                      |
| `'TOTAL.CUSTOMERS'`         | The annual number of total customers served in each state                                                                            |
| `'POPULATION'`              | The total population of each state for each year                                                                                     |

# Data Cleaning and Exploratory Data Analysis
## Data Cleaning
To clean my data, first, I created a dataset containing only the relevant columns listed in the table above. Although this made it so I had less data to work with, it made working with this dataset much less overwhelming and allowed me to more closely analyze the columns I kept. Next, I combined the columns `'OUTAGE.START.DATE'` and `'OUTAGE.START.TIME'` into `'OUTAGE.START'` and  `'OUTAGE.RESTORATION.DATE'` and `'OUTAGE.RESTORATION.TIME'` into `'OUTAGE.RESTORATION'`. Combining these columns allows me to store Pandas Timestamp objects in the `'OUTAGE.START'` and `'OUTAGE.RESTORATION'` columns, which represent exact moments in time instead of just being python `String` objects representing dates/times. The first few rows of the resulting DataFrame with a few of the relevant columns are below.

|   YEAR | U.S._STATE   | CLIMATE.REGION     | OUTAGE.START        | OUTAGE.RESTORATION   |
|-------:|:-------------|:-------------------|:--------------------|:---------------------|
|   2011 | Minnesota    | East North Central | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |
|   2014 | Minnesota    | East North Central | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |
|   2010 | Minnesota    | East North Central | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |
|   2012 | Minnesota    | East North Central | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |
|   2015 | Minnesota    | East North Central | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |

## Univariate Analysis
To analyze the distribution of outages over the regions in the `'CLIMATE.REGION'` column, I created a choropleth map showing the proportion of outages recorded in each region. The states included in each region are taken from the `'U.S._STATE'` column of the dataset. 

<iframe
    src="assets/regions-dist.html"
    width="800"
    height="600"
    frameborder="0"
></iframe>

This plot shows that the regions that had the most power outages (recorded in this dataset) are the Northeast, the West, and the South. This could be for a variety of reasons, and is not necessarily reflective of where all power outages tend to occur. However, since these are climate regions, there could be an association between a more volatile climate (in the Northeast, West, and South) and the amount of power outages.

## Bivariate Analysis
To see if some outage causes tend to correlate with longer outage durations, I created a box plot showing the distribution of the `'OUTAGE.DURATION'` column for each cause category present in the `'CAUSE.CATEGORY'` column.

<iframe
    src="assets/duration-dist.html"
    width="800"
    height="600"
    frameborder="0"
></iframe>

This plot shows that within the dataset, outages caused by severe weather and fuel supply emergencies have, on average, longer outage durations than those of other causes, which tend to be quite short. Additionally, this plot shows that the majority of outages in the dataset are very short, with a few extreme outliers present, particularly for outages caused by a fuel supply emergency. If we make the assumption that longer outages are more severe, then there is a visible correlation between outage cause and outage severity within this dataset.

## Interesting Aggregates
I performed several aggregations on this dataset to look for trends between climate regions, outage causes, months of the year, and outage durations. One aggregation I performed was counting the total number of outages in each region for each cause category, which resulted in the pivot table below.

| CLIMATE.REGION     |   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
|:-------------------|--------------------:|------------------------:|---------------------:|------------:|----------------:|-----------------:|--------------------------------:|
| Central            |                   7 |                       4 |                   38 |           3 |               2 |              135 |                              11 |
| East North Central |                   3 |                       5 |                   20 |           1 |               2 |              104 |                               3 |
| Northeast          |                   5 |                      14 |                  135 |           1 |               4 |              176 |                              15 |
| Northwest          |                   2 |                       1 |                   89 |           5 |               2 |               29 |                               4 |
| South              |                  10 |                       7 |                   28 |           2 |              42 |              113 |                              27 |
| Southeast          |                   5 |                       0 |                    9 |           0 |               5 |              118 |                              16 |
| Southwest          |                   5 |                       2 |                   64 |           1 |               1 |               10 |                               9 |
| West               |                  21 |                      17 |                   31 |          28 |               9 |               70 |                              41 |
| West North Central |                   1 |                       1 |                    4 |           5 |               2 |                4 |                               0 |

This pivot table shows that there is variation between how common each outage cause is between climate regions. For example, there were 176 outages caused by severe weather recorded in the Northeast, but there only 70 outages caused by severe weather recorded in the West. 

# Assessment of Missingness
## NMAR Analysis
In this dataset, there are several columns with non-trivial missingness, one of which is `'OUTAGE.RESTORATION'`. This column is likely NMAR because in the data generating process, this column came from whichever crew/company was fixing the outage, which varies widely over the dataset. If an outage restoration crew failed to report the time or date of restoration, then the resulting value would be missing. To explain this missingness, one could collect data about what company fixed each outage, or what that company's restoration documentation process looks like.

## Missingness Dependency
Another column in this dataset with non-trivial missingness is `'CUSTOMERS.AFFECTED'`. To analyze if the missingness in this column was dependent on other columns I performed permutation tests to see if the distributions of columns grouped by missing `'CUSTOMERS.AFFECTED'` values and non-missing `'CUSTOMERS.AFFECTED'` were statistically different. I used TVD as my test statistic. First, I performed a permutation test to see if the missingness of `'CUSTOMERS.AFFECTED'` was MAR dependent on `'CAUSE.CATEGORY'`:

Is `'CUSTOMERS.AFFECTED'` MAR dependent on `'CAUSE.CATEGORY'`?
- Null hypothesis: The distribution of `'CAUSE.CATEGORY'` is the same when `'CUSTOMERS.AFFECTED'` is or is not missing
- Alternate hypothesis: The distribution of `'CAUSE.CATEGORY'` when `'CUSTOMERS.AFFECTED'` is missing is different from the distribution when `'CUSTOMERS.AFFECTED'` is not missing.

As a result, I got a p-value of $0.0$, so I rejected the null hypothesis at a significance level of $p<0.05$. This allows me to conclude that the missingness in `'CUSTOMERS.AFFECTED'` is likely MAR dependent on `'CAUSE.CATEGORY'`. In addition, before shuffling my dataset, I plotted the distribution of cause categories for the two groups (missing and not missing):

<iframe
    src="assets/missing-dist.html"
    width="800"
    height="600"
    frameborder="0"
></iframe>

This plot shows the distribution of outages in each cause category for outages with a missing value in the `'CUSTOMERS.AFFECTED'` column and those with a non-missing value. This plot shows that a large proportion of outages caused by an intentional attack have missing values in the `'CUSTOMERS.AFFECTED'` column. 

To further analyze the relationship between the values in the `'CUSTOMERS.AFFECTED'` column and the `'CAUSE.CATEGORY'` without introducing bias, I must account for the missingness in the `'CUSTOMERS.AFFECTED'` column.

Additionally, I performed a permutation test to see if the missingness in `'CUSTOMERS.AFFECTED'` was MAR dependent on the `'CLIMATE.CATEGORY'` column:

Is `'CUSTOMERS.AFFECTED'` MAR dependent on `'CLIMATE.CATEGORY'`?
- Null hypothesis: The distribution of `'CLIMATE.CATEGORY'` is the same when `'CUSTOMERS.AFFECTED'` is or is not missing
- Alternate hypthesis: The distributions of `'CLIMATE.CATEGORY'` when `'CUSTOMERS.AFFECTED'` is and isn't missing are different

As a result, I got a p-value of $0.318$, so I failed to reject the null hypothesis at a significance level of $p<0.05$. This means that the missingness in `'CUSTOMERS.AFFECTED'` is most likely *not* MAR dependent on `'CLIMATE.CATEGORY'`. 

# Hypothesis Testing
## Permutation Test
I performed a permutation test to answer the question, do outages that occured in the northeast and outages that occured elsewhere come from the same distribution of outage causes? My null hypothesis was, "the distribution of outage causes for outages that occured in the northeast is the same as those that occured elsewhere. The difference between the two observed samples is due to chance". My alternate hypothesis was, "the distribution of outage causes for the two regions are different". Since I was comparing two categorical distributions, I used TVD as a test statistic. As a result, I got a p-value of $0.0$, so I rejected the null hypothesis. This means that the distribution of causes for outages in the Northeast and outages elsewhere are likely not the same, which could imply that certain outage causes happen in one climate region more than the rest. I chose to perform this specific permutation test to observe regional characteristics associated with the distribution of outage causes.

# Framing a Prediction Problem
## Problem Identification
With my baseline and final models, I will try to predict the cause category of an outage that has just started. `'CAUSE.CATEGORY'` is a categorical column, so this is a classification prediction problem. Since there are seven cause categories, this is a multiclass classification problem. 

Since I am predicting an outage's cause at the time that it starts, I can't use data that was collected about the effects of an outage as features in my model (e.g. `'CUSTOMERS.AFFECTED'`). So, I have to use data about the time/place/climate where the outage occurs. For my models, the response variable is the cause category, where each unique category is labeled with an integer. 

To evaluate my models, I used the f1-score instead of accuracy. This is because the distribution of outages over all the cause categories is uneven, so the f1-score gives a better metric for how the model performs with the uneven category weights.

# Baseline Model
In my baseline model, I used a decision tree to predict the cause category for outages. For features, I used `'YEAR'` (ordinal), `'MONTH'` (ordinal), `'CLIMATE.REGION'` (nominal), `'ANOMALY.LEVEL'` (Quantitative), and `'TOTAL.SALES'`(Quantitaive) as features. I used `'YEAR'` because outage causes have changed over time due to infrastructure, technology, and countless other reasons. I used `'MONTH'`, `'CLIMATE.REGION'`, and `'ANOMALY.LEVEL'` because these features correlate with the climate at the time an outage occurs, and climate can effect outage causes. Lastly, I used `'TOTAL.SALES'` because increased electricity consumption can correlate with outages due to system errors, equipment failure, or fuel supply emergencies. 

Since `'CLIMATE.REGION'` is a nominal feature, I encoded it using one-hot encoding to create binary features for each region. 

My baseline model had a training f1-score of $\sim 0.56$ and a testing f1-score of $\sim 0.53$, both of which aren't very high! This could be because I arbitrarily picked a tree depth of 3, which isn't necessarily the best value! Also, it's a pretty complicated prediction task for a relatively simple model, so it makes sense that it wouldn't do that well. 

# Final Model
For my final model, I used a random forest of decision trees. For features, I used the same features as my baseline model, but I transformed `'ANOMALY.LEVEL'` into quartiles and I transformed `'TOTAL.SALES'` into percentiles. These transformations into new features improved my model because the quantiles should have a closer correlation to the target category than the raw data. To improve my model further, I performed 5-fold cross-valuidation to find the best maximum depth for the decision trees in the random forest and the number of trees to put in the random forest. As a result of the 5-fold cross-validation, I got that the best `max_depth` value is 45, and the best number of trees is 95. 

My final model had a training f1-score of $\sim 0.92$ and a testing f1-score of $\sim 0.67$, showing some improvement from the baseline model. My final model performed better than my baseline model because the random forest and optimized hyperparameters allowed the model to score better on the training data, causing an increase in score for the testing data.

# Fairness Analysis

