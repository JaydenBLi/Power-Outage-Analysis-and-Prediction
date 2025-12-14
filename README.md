# Introduction
This project uses a dataset from the U.S. Department of Energy that documents major electric power outages across the United States. Each row represents one outage and includes information on when it occurred, what caused it, where it happened, and how many customers were affected. Understanding outage behavior is increasingly important as extreme weather and rising energy demand place more stress on the grid.

## Research Question
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

# Assessment of Missingness

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis
