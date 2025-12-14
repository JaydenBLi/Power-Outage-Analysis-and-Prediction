# Introduction  
This project uses a dataset from the U.S. Department of Energy that documents major electric power outages across the United States. Each row represents one outage and includes information on when it occurred, what caused it, where it happened, and how many customers were affected. Understanding outage behavior is increasingly important as extreme weather and rising energy demand place more stress on the grid.

### Research Question
- What trends exist in U.S. power outages over time, and can outage characteristics be used to predict how many customers will be affected by a future outage?  
  
This question matters because predicting outage severity helps utilities and emergency responders allocate resources, improve grid resilience, and prepare for extreme events.
  
The dataset contains 1536 rows and 56 columns. The most relevant columns for the project are:
- OUTAGE.START – When the outage began.

- YEAR and MONTH – Temporal patterns related to seasons and long-term trends.

- CAUSE.CATEGORY – Broad cause of the outage (e.g., severe weather, equipment failure).

- NERC.REGION – Geographic region involved.

- ANOMALY.LEVEL – Climate anomaly indicator for the event.

- CUSTOMERS.AFFECTED – Number of customers impacted (the prediction target).

- POPULATION, POPDEN_URBAN, POPDEN_RURAL – Demographic factors affecting potential outage scale.  
  
# Data Cleaning and Exploratory Data Analysis  
Before analysis and modeling, the outage dataset required several cleaning steps to convert the raw fields into a consistent and usable format. Each step reflected issues arising from the data-generating process—that is, how outage records were originally collected, logged, and reported.  
  
**1. Dropping metadata rows and unused columns**    
The Excel file contained several descriptive rows at the top (e.g., documentation notes, column descriptions). Because these are not actual observations, they would disrupt parsing and produce incorrect column types. These were removed.

**2. Combining date and time fields into timestamps**  
The dataset originally stored outage start and restoration information in separate date and time columns. They were combined into new columns containing one value with the date and the time.  
  
**3. Removing rows with missing or invalid start times**    
Some outages lacked a valid start timestamp due to incomplete reporting or system logging failures. These rows cannot be used in any temporal analyses or in a predictive model that relies on known outage start characteristics. Since the research question depends on the time of the outage, these rows were dropped.  

### Final Cleaned Dataset  
Below is a table containing a few rows and columns of the cleaned dataset.  
  
|   YEAR |   MONTH | U.S._STATE   | OUTAGE.START        | OUTAGE.RESTORATION   |
|-------:|--------:|:-------------|:--------------------|:---------------------|
|   2011 |       7 | Minnesota    | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |
|   2014 |       5 | Minnesota    | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |
|   2010 |      10 | Minnesota    | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |
|   2012 |       6 | Minnesota    | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |
|   2015 |       7 | Minnesota    | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |
  
### Data Vizualizations  
<iframe
  src="assets/fig_customers_log.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
This is a histogram of the column 'CUSTOMERS.AFFECTED' in the dataset, with a log scale applied. The log scale was applied sdue to the original distribution being extremely skewed, as there are many more minor outages than large ones.  
  
<iframe
  src="assets/fig_box_bivariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>  
These side-by-side box plots display the relationship between the cause of the outage and the duration of the outage. From this we can see clearly that the two variables are not independent, and that different outage causes have different impacts, and that fuel supply emergencies tend to be the most severe with the rest having less extreme differences.  
  
| CAUSE.CATEGORY                |   count |   mean_duration |   median_duration |   max_duration |
|:------------------------------|--------:|----------------:|------------------:|---------------:|
| fuel supply emergency         |      50 |       13484     |            3960   |         108653 |
| severe weather                |     759 |        3883.99  |            2460   |          49320 |
| public appeal                 |      69 |        1468.45  |             455   |          11867 |
| equipment failure             |      57 |        1816.91  |             221   |          78377 |
| system operability disruption |     126 |         728.87  |             215   |          23187 |
| islanding                     |      46 |         200.545 |              77.5 |           1254 |
| intentional attack            |     418 |         429.98  |              56   |          21360 |
  
The above table provides more detail on the relationship between outage cause and outage duration.  
  
# Assessment of Missingness  
### NMAR(Not Missing at Random) Analysis  
**DEMAND.LOSS.MW**: small demand loss outages are less likely to have an accurately reported value; There may be too many 'small' outages for single one of them to be surveyed and reported. Thus, smaller losses are more likely to be missing, making the values for this column NMAR.  
  
### Missingness Dependency
A permutation test was performed to test whether or not DEMAND.LOSS.MW depended on CUSTOMERS.AFFECTED, using the test statistic of the mean values of missing vs not missing. The attained p-value was **0.0005**. This provides very strong evidence that the missingness of DEMAND.LOSS.MW is not independent from CUSTOMERS.AFFECTED.  
  
<iframe
  src="assets/fig_box_missingness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>  
Although the difference in the box plots appears to be small, due to the nature of the log scale the difference is much larger than percieved. 
  
In addition, a second permuation test was performed comparing the missingness of DEMAND.LOSS.MW compared to OUTAGE.DURATION, finding a p-value of **0.7955**. This rather high p-value suggests that the missingness of DEMAND.LOSS.MW does not depend on OUTAGE.DURATION.
  
# Hypothesis Testing
- H0: The distribution of outage durations with a cause of 'severe weather' is the same as the distribution of outage durations with a cause of 'equipment failure'  
- Ha: 'severe weather' outages tend to have longer durations than 'equipment failure' outages.  
Test Statistic: Difference in median of each  
Significance level: <0.05  
Observed statistic: 2239.0
p-value: 0.0  
  
The observed difference in median outage duration between Severe Weather and Equipment Failure outages was substantial and positive.
In a permutation test with 3,000 iterations, none of the permuted differences exceeded the observed difference.
Therefore, we reject the null hypothesis and conclude that outages caused by severe weather tend to last longer than outages caused by equipment failure.  

# Framing a Prediction Problem  
### Predicting the Number of Customers Affected by an Outage(Regression)  
    
- Response Variable: CUSTOMERS.AFFECTED  
- Evaluation Metric: RMSE; the primary drawback of this loss function is that outliers are heavily affected. However, since we would be trying to estimate the number of affected customers to create a suitable response, it would be much better to "overreact" than to be underprepared to address the outage.  
  
There are certain variables we would be unable to use for this prediction problem. Any feature of our dataset that would be measured after the outage start time would be unavailable to those attempting to predict the severity of an ourage and therefore cannot be used in creating a model.  
  
# Baseline Model  
A linear regression model was trained to predict CUSTOMERS.AFFECTED using only features available at the start of an outage. The model included three predictors:  
  
Quantitative (1): MONTH (passed through unchanged)  
  
Nominal (2): CAUSE.CATEGORY, NERC.REGION (both one-hot encoded)  
  
No ordinal features were used. All preprocessing (one-hot encoding) and model training were implemented inside a single sklearn Pipeline.  
  
Performance:  
  
Train RMSE: ~265,356  
  
Test RMSE: ~297,478  
  
The train/test gap is small, so the model generalizes reasonably, but the overall RMSE is large relative to the scale of outages. This indicates the baseline model underfits the data and is not very accurate, which is expected for a simple linear model with only a few features. It serves primarily as a starting benchmark for more complex models.  
  
# Final Model
For my final model, I expanded the feature set and engineered additional transformed features to better capture structure in the data while keeping the model consistent with the information available at prediction time. In addition to the features used in the baseline model (MONTH, CAUSE.CATEGORY, NERC.REGION), I added:  
  
- YEAR, to capture long-term structural changes in grid infrastructure, population growth, and reporting practices that may influence the magnitude of outages over time.  
  
- NOMALY.LEVEL, which quantifies how unusual the climate conditions were during the outage period; outages occurring under extreme or anomalous climate conditions may affect larger areas and more customers.  
  
I also created two engineered numeric features using transformations applied within the pipeline:  
  
- Standardized (StandardScaler) versions of MONTH and YEAR  
These help the model represent seasonal and long-term temporal effects on a common scale, which is appropriate because month and year operate on very different numeric ranges.  
  
- A quantile-transformed (QuantileTransformer) version of ANOMALY.LEVEL  
Climate anomalies are highly skewed, with many mild events and fewer extreme values. Quantile transformation creates a more uniform distribution of this variable, helping the linear model learn smoother relationships.  
  
These transformations do not add information unavailable at prediction time; they simply reshape the existing numeric features into forms that expose patterns more clearly to the model.  
  
**Modeling Algorithm & Hyperparameter Choice**  
  
I chose Ridge Regression as the final model. It is a linear model like the baseline but includes L2 regularization, which stabilizes estimates in the presence of multicollinearity (expected here due to correlated geographic and climate variables) and prevents overly large coefficients.  
  
To determine the best level of regularization, I searched over different values of the alpha hyperparameter using GridSearchCV with 3-fold cross-validation. The best performing value was:  
  
- alpha = 100.0  
  
This represents a fairly strong level of regularization, which suggests that the raw and engineered features contain correlated or noisy relationships, and the model benefits from shrinking coefficient magnitudes.  
  
**Improvement Over the Baseline**  
  
The baseline model (ordinary least squares with minimal features) achieved:  
  
- Baseline train RMSE: ~265,356  
  
- Baseline test RMSE: ~297,478  
  
The final Ridge model achieved:  
  
- Final train RMSE: ~267,793  
  
- Final test RMSE: ~292,533  
  
Although the training error increased slightly—as expected when adding regularization—the test RMSE decreased, showing that the final model generalizes better than the baseline. The improvement is modest, but meaningful: the final model is less overfit and benefits from using richer features plus regularization to better capture the underlying structure in the outage data.  
  
This outcome is consistent with the data-generating process: customer impact depends on seasonal factors (MONTH), long-term trends (YEAR), climate anomalies (ANOMALY.LEVEL), and inherent differences across regions and causes. Including these effects while controlling model complexity results in improved prediction performance relative to the baseline.  

# Fairness Analysis  
I examined whether my final regression model performs differently for outages caused by Severe Weather compared to those caused by Equipment Failure. These are two large, meaningful groups that reflect different physical mechanisms behind outages.  
  
Using the test set, I computed RMSE separately for the two groups:  
  
- RMSE_SevereWeather = 284264.991  
  
- RMSE_EquipmentFailure = 171952.096  
  
- Observed difference = 112312.894  
  
I then performed a permutation test, where I repeatedly shuffled the cause labels between these two groups and recomputed the RMSE difference under the null hypothesis that the model’s errors are unrelated to cause category. Out of 2000 permutations, the proportion with a difference at least as large as T_obs was **0.5155**  
  
This suggests that the observed RMSE difference can reasonably be explained by random variation, and I do not find strong evidence that the model is substantially less accurate for Severe Weather than for Equipment Failure outages.  
