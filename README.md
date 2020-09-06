# The capstone project

## Introduction 
### Background
The Seattle Police Department (SPD) provides collisions records since 2004, containing the severity of each road collision (0 property damage/ 1 personal injury) with additional information, such as the location, the weather and road conditions, and more. 
Preventing road collisions is a major concern for public authorities. Therefore, it is important for the SPD to accurately predict whatever a serious accident will occur given all available data. It is also important to identify the factors causing serious accidents, as these could be eliminated or mitigated before a collision occurs.

### Problem
This project aims to predict the severity of a collision given other factors, such as the wheatear condition, the road condition, the number of vehicles, and persons in the road.

### Interest
Predicting the severity of a collision can be of interest for a warning system. The SPD could issue a warning on the highway signs when a serious accident is predicted (severity 1, personal injury). This information allows the drivers to pay more attention when notified. 

## Data
### Sources
In this study, the Seattle collisions records from 2004 to 2020 were used. The data can be found in [here](https://data-seattlecitygis.opendata.arcgis.com/). The description of each feature (metadata) can be found [here](https://www.seattle.gov/Documents/Departments/SDOT/GIS/Collisions_OD.pdf)
The source contains a mixture of categorical and continuous variables.

### Feature selection
The entire set was immediately divided into a train and test set. Decisions on feature dropping and data cleaning were taken by analyzing only the test set and applied later on to both the train and test sets. This choice was motivated to avoid lookahead bias. Table 1 shows a summary of the test set.

![Tab1](figures/Fig1.jpg)

[Table 1. Train set summary]

Table 2 summarizes the decisions taken for selecting the features.

First, the features identical to the target or containing the same type of information were eliminated, to prevent the models to use the target as a feature.

Second, some features are missing an appropriate description and have a large percentage of missing values. These features were dropped.

Third, features containing administrative information or collision details available only after a collision occurred were eliminated. 

Fourth, the INCDATE feature was eliminated as contains the collision date as the INCDTTM feature. 

Fifth, the location feature was eliminated because it contains information that is similar to the X and Y GPS coordinates.

![Tab2](figures/Fig2.jpg)

[Table 2. Dropped features and reasons for dropping]

### Data cleaning

After dropping the features, several other categorical features had a large ratio of missing values: INATTENTIONIND, PEDROWNOTGRNT, SPEEDING. These features contain only the value Y and nan (not a number). For them, it was speculated that NaN corresponds to no (N), as the SPD agent could have decided not to fill these fields when the condition was negative. All Ns were converted to 0 and all Y to 1, transforming these three features from categorical to numerical. 

The feature UNDERINFL contains 4 values: N, 0, Y, 1. In this case, N was converted to 0 and Y to 1, also transforming UNDERINFL into a numerical feature. For UNDERINFL, Y was not the only entry present in the original dataset, and nan entries were not assumed to indicate a negative condition.

LIGHTCOND, ROADCOND, and WEATHER features already contain a value ‘Unknown’. For them, nan entries were converted to ‘Unknown’.

The number of missing values in the JUNCTIONTYPE was reduced by using valid JUNCTIONTYPE entries of other records with the same X and Y coordinates. This decreased the number of missing values for JUNCTIONTYPE from 3.2% to 1.3% in the train set and from 3.3% to 1.6% in the test set.

As most predictive models only accept numerical values, the information of the INCDTTM feature was converted into 4 numerical features, namely the year, the month, the day of the week, and the hour. 

Box plots where used to detect the outliers for features with a large range of variation (Figure 1). The collisions involving up to 81 persons, in a lane segment greater than 525241 and a crosswalk number larger than 5239700 are outliers and were eliminated. In total, only 3 records of the train set were eliminated.

![Fig1](figures/Fig3.jpg)

[Figure 1. Box plots for numerical features with a large range of variation]


Table 3 summarizes the train set after feature selection, data cleaning and conversion of categorical features into numerical ordinal features. 

![Tab3](figures/Fig4.jpg)
 
 [Table 3. Train set summary after feature dropping and data cleaning]

 ## Methodology

 ### Exploratory Data Analysis

For each feature, the recods in the train set were grouped by the target value (0/1) to assess if the means group values are significantly different (t-test with a p value <0.05). It was found that the mean group values are significally different, except for the month feature. As result, month was dropped (Figure 2). This result was unexpected, because severe accidents with personal injuries were expected to occour more frequently in fall or winter, where wheater and road conditions can be worse.

![Fig2](figures/Fig5.jpg)
 [Figure 2. Bar chart for month feature grouped by severity code]

As expected, collisions involving a pedastrian or a bycicle more often results in a personal injuries compared to collisions with no pedastrian or bycicle involved (Figure 3 and 4). When forecasting the severity of a collision is therfore important to take into account the location of a possible accident, and whatever this location has a pedastrian area.


![Fig3](figures/Fig6.jpg)
[Figure 3. Bar chart for PERSONCOUNT feature grouped by severity code]

![Fig4](figures/Fig7.jpg)
[Figure 4. Bar chart for PEDCOUNT feature grouped by severity code]

Before jumping into modelling a correlation analysis was performed, to identify strongly correlated features (absolute correlation value > 0.9). I one feature is strongly correlated to another it can be dropped to reduce the dimensionality of the feature set. 
The feature correlation matrix computed from the train set is shown in Figure 5. Only ADDRTYPE was found to be strongly correlated to JUNCTIONTYPE (-0.92). Since JUNCTIONTYPE had more missing values than ADDRTYPE in the initial dataset, the JUNCTIONTYPE feature was dropped.

![Fig5](figures/Fig8.jpg)
[Figure 5. Feature correlation matrix]

 ### Modelling

The train set in unbalanced: the 69.8% of the records have a target equal to 0 (property damage). Therfore, the weights to apply to each target during traing were set inversely proportional to class frequencies.This chociche mitigates the model bias, where only the most frequent class is chososen to maximize model accuracy.

Before modelling, the feature values in the train and test set were also scaled using the feature's train set mean and standard deviation.

Four models were fitted to the train set and compared using the test set:
* Decision tree classifier: node splitting criterion (gini,entropy) and tree max depth were tuned
* XGBoost classifier: gamma parameter for tree splitting and tree max depth were tuned
* Random forest: node splitting criterion (gini,entropy) and tree max depth were tuned
* Soft voting classifier: build using the probabilities computed from tree best models above

The parameters were tuned using the average 3 fold cross validation score on the train set. The logarimic loss was used as score function because it puts more emphasis on probabilities than other metrics.

 ## Results

Table 4 shows the metrics on the test set for each model. In this case, it is more important to correctly predict the collision with causing a personal injury that correctly predict the collisions with property damage. The decision tree classifier correctly predicts more severity 2 collisions (76% of all severity 2 collisions in the test set).


![Tab4](figures/Fig9.jpg)

 ## Discussion

From the results it is clear that models with an higr accuracy on severity 1 collisions tends to have a lower accuracy on severity 2 collisions. XGBoost is the best model for severity 1 collins and has the best accuracy and f1 scores, but does a worse job if severity 2 collisions. The decision tree has the opposite behaviour. The voting model has an average performance for both collision types, between XGBoost and decision tree.   

 ## Conclusions

 In this study, the  Seattle Police Department collisions records were analyzyd with the aim of building a model that predicts collisions causing personal injuries from data available before a collosion occours. 
 this model can be to issue  a warning on the highway signs when a serious accident is predicted, to warn drivers to pay more attention in dangerous conditions.
 After data cleaning and selecting only the feature describing the pre-collisions conditions, a decision tree classifier was found to be able to predict severe accidents with 76% accuraracy.
The data set was strongly umbalanced towards collisions with severity code 1 (property damage). Additional records with severity code 2 would help to improve the model performance for this type of accidents
 Some of the data were missing a description, while for others with a large amount of missing values assumprions were made, In these cases it would have been helpful to have less missing value fields.



