# Power Outage Severity Analysis

This project analyzes factors that influence the severity of power outages in the United States.

The goal is to understand which conditions lead to longer outages and to build a model that predicts whether an outage will be high severity.

Author: Cameron Chen

# Introduction
The power outages dataset that I have chosen records data from power outages across the United States. Each power outage includes numerous data variables including power outage start/stop times, region, and customers affected. The question I chose to center of my project around was what factors affect outage severity the most. In this dataset, there are 1535 rows (outages) and 57 columns (factors). The columns I decided were most relevant to my question were OUTAGE.START.DATE, OUTAGE.START.TIME, OUTAGE.RESTORATION.DATE, OUTAGE.RESTORATION.TIME. Those four variables told me the exact day and time that the outages started and ended. Additionally, other variables like ANOMALY.LEVEL (unusualness of climate), CUSTOMERS.AFFECTED (# of customers within outage), CAUSE.CATEGORY (main cause of outage), CLIMATE.REGION (area of US outage took place) provided information on what were the main causes for each outage. 

# Data Cleaning and Exploratory Data Analysis
There were several data cleaning steps to ensure that the dataset was prepped for analysis and that the variables I intended to use were cleaned up. The first part of my cleaning was to determine the exact duration of each outage. I created a new column OUTAGE.DURATION and took the difference of OUTAGE.START.DATE + OUTAGE.START.TIME and OUTAGE.RESTORATION.DATE + OUTAGE.RESTORATION.TIME to determine the exact outage length. Using the new OUTAGE.DURATION variable, we were able to derive a binary variable, HIGH.SEVERITY, for outages greater than the median outage duration. A SEASONS column was also created from OUTAGE.START.DATE to see if there were trends based on the season of the year. 


First 5 Rows of Cleaned Dataset
| CAUSE.CATEGORY     | CLIMATE.REGION     |   ANOMALY.LEVEL |   CUSTOMERS.AFFECTED |   DURATION.HOURS |   HIGH.SEVERITY |
|:-------------------|:-------------------|----------------:|---------------------:|-----------------:|----------------:|
| severe weather     | East North Central |            -0.3 |                70000 |       51         |               1 |
| intentional attack | East North Central |            -0.1 |                  nan |        0.0166667 |               0 |
| severe weather     | East North Central |            -1.5 |                70000 |       50         |               1 |
| severe weather     | East North Central |            -0.1 |                68200 |       42.5       |               1 |
| severe weather     | East North Central |             1.2 |               250000 |       29         |               1 |



Plot 1 - Distribution of Outage Duration

Embed at least one plotly plot you created in your notebook that displays the distribution of a single column (see Part 2: Report for instructions). For each plot that you embed, include a 1-2 sentence explanation about your plot, making sure to describe and interpret any trends present.

Plot 1 - Distribution of Outage Duration Plot(Univariate)

<iframe
    src="assets/duration_dist.html"
    width="800"
    height="600"
    frameborder="0"
</iframe>

The distribution of outage duration is highly right-skewed, with most outages lasting a relatively short time and a small number lasting significantly longer. This suggests that while most outages are resolved quickly, extreme events can lead to disproportionately long durations.


Plot 2 - Cause vs Duration Distribution Plot(Bivariate)

<iframe
    src="assets/cause_vs_duration.html"
    width="800"
    height="600"
    frameborder="0"
</iframe>

Outage duration varies substantially across different cause categories. In particular, outages caused by severe weather tend to have longer durations compared to other causes, suggesting that the underlying cause of an outage is an important factor in determining its severity.


Plot 3 - Severity based on Region Plot(Aggregate)

<iframe
    src="assets/severity_by_region.html"
    width="800"
    height="600"
    frameborder="0"
</iframe>

Severity based on Region Table
| CLIMATE.REGION     |   HIGH.SEVERITY |
|:-------------------|----------------:|
| East North Central |       0.808824  |
| Central            |       0.597765  |
| Northeast          |       0.556634  |
| Southeast          |       0.547297  |
| South              |       0.527778  |
| Northwest          |       0.362745  |
| West               |       0.29902   |
| Southwest          |       0.152941  |
| West North Central |       0.0714286 |

The grouped table and graph quantify the variation in power outage severity across the US. The severity levels are divided into 9 distinct regions and displays that different regions have a higher proportion of severe outages and patterns that they are more at risk.