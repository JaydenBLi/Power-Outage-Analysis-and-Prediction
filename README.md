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

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis
