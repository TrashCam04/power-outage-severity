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


# Step 5: Framing a Prediction Problem
The prediction problem is to create a model that can predict whether a power outage will be high severity or not. The prediction model type is a binary classification problem with `HIGH.SEVERITY` being either 1 (high severity) or 0 (not high severity). `HIGH.SEVERITY` was the response variable chosen because as seen in other sections of the project, it has the biggest impact on measuring how impactful an outage is.

The features used for prediction are: 
- `CAUSE.CATEGORY`
- `CLIMATE.REGION`
- `SEASON`
- `ANOMALY.LEVEL`
- `POPPCT_URBAN`
- `YEAR`

The particular set of features are chosen because they are relevant to serverity or influence it in some way. In this model, accuracy is used as the primary metric. This is because the response variable being binary makes it relatively balanced and a strong measure of model performance. Binary is a very black and white classifier and it'll be straightfoward to measure model accuracy through it. 



The prediction is assumed to be made at the time an outage occurs, before its severity is known. Therefore, only features that would be available at that time are used. For example, we do not include variables like outage duration, since that is only known after the outage has ended.

To evaluate the model, we will use **accuracy** as the primary metric. Accuracy is appropriate because the response variable is binary and relatively balanced, making it a straightforward measure of overall model performance. It provides an interpretable measure of how often the model correctly classifies outages as high severity or not.

# Step 6: Baseline Model
The features used for prediction are: 
- `CAUSE.CATEGORY` (nominal)
- `ANOMALY.LEVEL` (quantitative)
- `POPPCT_URBAN` (quantitative)

All of the categorical variables were one-hot encoded that allowed them to be converted to a numerical form for better prediction modeling. Numerical variables were not transformed. We split the data into training and testing sets using a 75/25 split. The model was trained on the training set and evaluated on the test set to assess its ability to generalize to unseen data.

Model accuracy: 0.8

This baseline model performs reasonably well and captures some important relationships between outage characteristics and severity. However, it uses a limited set of features and a relatively simple model, so there is still room for improvement through additional feature engineering and more complex modeling approaches.


# Step 7: Final Model
For the final model , 2 additional features were added to hyperparameter tuning to improve the baseline model. 
The two new features were:
- `ANOMALY_ABS`(quantitative): the absolute value of `ANOMALY.LEVEL`
- `URBAN_HIGH`(nominal): a binary indicator for whether `POPPCT_URBAN` is above the median

The feature `ANOMALY_ABS` captures the magnitude of climate anomalies regardless of direction. From a data generating perspective, extreme weather conditions can both contribute to more severe outages. Taking the absolute value allows the model to better capture the intensity of abnormal conditions rather than their direction.

The feature `URBAN_HIGH` distinguishes between highly urbanized and less urbanized areas. This is useful because outages in densely populated regions may affect more infrastructure and people, potentially increasing severity. This binary transformation simplifies the relationship and allows the model to capture differences between urban and non-urban environments more effectively.

The final model achieves an accuracy of approximately 0.80 on the test set, which is similar to the baseline model. While the overall accuracy did not increase, the final model incorporates additional features that better reflect the underlying data generating process, such as the magnitude of anomaly levels and urbanization effects. These features provide a more meaningful representation of the factors that influence outage severity, making the model more interpretable and potentially more robust to different data scenarios.

# Step 8: Fairness Analysis
To assess fairness, we compared the model’s accuracy between two groups:

- **Group X:** outages in the `East North Central` climate region
- **Group Y:** outages in all other climate regions

We chose accuracy as the evaluation metric, since it is the same metric used to evaluate overall model performance and gives a direct way to compare how often the model is correct for each group.

Our hypotheses were:

- **Null hypothesis:** the model has the same accuracy for outages in the East North Central region as it does for outages in all other regions.
- **Alternative hypothesis:** the model’s accuracy differs between the East North Central region and all other regions.

We used the difference in group accuracies as the test statistic and a significance level of 0.05. The observed difference in accuracy was approximately 0.0233, and the permutation test produced a p value of 0.81.

Since the p value is much larger than 0.05, we fail to reject the null hypothesis. This suggests that there is not strong evidence of a difference in model accuracy between the East North Central region and the rest of the dataset. Based on this test, the model appears reasonably fair with respect to this grouping.

<iframe
    src="assets/permutation_accuracy.html"
    width="800"
    height="600"
    frameborder="0">
</iframe>

The permutation distribution is centered near zero, which represents the expected difference in accuracy under the null hypothesis that the model performs similarly across groups. The observed difference lies near the center of this distribution, which is consistent with the large p value and suggests that the observed gap in accuracy could easily occur by chance.
