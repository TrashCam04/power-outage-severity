# Power Outage Severity Analysis

This project analyzes factors that influence the severity of power outages in the United States.

The goal is to understand which conditions lead to longer outages and to build a model that predicts whether an outage will be high severity.

Author: Cameron Chen

# Step 1: Introduction
The power outages dataset that I have chosen records data from power outages across the United States. Each power outage includes numerous data variables including power outage start/stop times, region, and customers affected. The question I chose to center of my project around was what factors affect outage severity the most. In this dataset, there are 1535 rows (outages) and 57 columns (factors). The columns I decided were most relevant to my question were OUTAGE.START.DATE, OUTAGE.START.TIME, OUTAGE.RESTORATION.DATE, OUTAGE.RESTORATION.TIME. Those four variables told me the exact day and time that the outages started and ended. Additionally, other variables like ANOMALY.LEVEL (unusualness of climate), CUSTOMERS.AFFECTED (# of customers within outage), CAUSE.CATEGORY (main cause of outage), CLIMATE.REGION (area of US outage took place) provided information on what were the main causes for each outage. 

# Step 2: Data Cleaning and Exploratory Data Analysis
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
    frameborder="0">
</iframe>

The distribution of outage duration is highly right-skewed, with most outages lasting a relatively short time and a small number lasting significantly longer. This suggests that while most outages are resolved quickly, extreme events can lead to disproportionately long durations.


Plot 2 - Cause vs Duration Distribution Plot(Bivariate)

<iframe
    src="assets/cause_vs_duration.html"
    width="800"
    height="600"
    frameborder="0">
</iframe>

Outage duration varies substantially across different cause categories. In particular, outages caused by severe weather tend to have longer durations compared to other causes, suggesting that the underlying cause of an outage is an important factor in determining its severity.


Plot 3 - Severity based on Region Plot(Aggregate)

<iframe
    src="assets/severity_by_region.html"
    width="800"
    height="600"
    frameborder="0">
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


# Step 3: Assessment of Missingness
Out of the relevant variables used in the analysis of outage severity, `CUSTOMERS.AFFECTED` is a strong candidate for being Missing Not At Random (MNAR). The missingness of this variable may depend on its true value, as outages affecting a very large number of customers may be more difficult to accurately measure and report. Similarly, smaller outages may not be recorded at all, leading to missing values.

To better determine whether this missingness mechanism could instead be considered Missing At Random (MAR), additional information such as data collection procedures, reporting systems, or measurement reliability during outages would be helpful. Without such information, MNAR remains a plausible explanation for the missingness in this column.



Since the p-value is less than 0.05, we reject the null hypothesis. This provides strong evidence that the missingness of `CUSTOMERS.AFFECTED` is dependent on `ANOMALY.LEVEL`, meaning the data is not Missing Completely At Random (MCAR). Instead, the missingness is likely Missing At Random (MAR), as it is associated with an observed variable.

<iframe
    src="assets/missingness_perm.html"
    width="800"
    height="600"
    frameborder="0">
</iframe>

The permutation distribution is centered around zero, representing the expected difference in means under the null hypothesis of independence. The observed test statistic lies far in the left tail of this distribution, indicating that the observed difference is unlikely to have occurred by chance. This provides strong visual evidence that missingness is related to anomaly level.


# Step 4: Hypothesis Testing
Null Hypothesis: Outages caused by severe weather have the same mean duration as outages caused by other causes.
Alternative Hypothesis: Outages caused by severe weather have a greater mean duration compared to outages caused by other causes. 
Significance Level: α = 0.05 (If p value > 0.05 then we accept the null hypothesis. Otherwise we reject) A significance level of 0.05 is the standard metric for measuring significance and is the metric that is chosen for this hypothesis test.
p value: p ≈ 0 (or < 0.001)

<iframe
    src="assets/severity_permutation.html"
    width="800"
    height="600"
    frameborder="0">
</iframe>

At a significance level of 0.05, the extremely small p value leads us to reject the null hypothesis. This suggests that outages caused by severe weather tend to last significantly longer than those caused by other factors and suggests that severe weather could be one of the bigger factors in outage length. 