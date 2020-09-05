# The capstone project

## Introduction 
### Background
The Seattle Police Department (SPD) provides collisions records since 2004, containing the severity of each road collision (0 property damage/ 1 personal injury) with additional information, such as the location, the weather and road conditions, and more. 
Preventing collisions is a major concern for public authorities. Therefore, it is important for the SPD to accurately predict whatever a serious accident will occur given all available data. It is also important to identify the factors causing serious accidents, as these could be eliminated or mitigated before a collision occurs.

### Problem
This project aims to predict the severity of a collision given other factors, such as the wheatear condition, the road condition, the number of vehicles, and persons in the road.

### Interest
Predicting the severity of a collision can be of interest for a warning system. The SPD could issue a warning on the highway signs when a serious accident is predicted (severity 1, personal injury). Such information allows the drivers to pay more attention when such notification is issued. 

## Data
### Sources
In this study, the Seattle collisions records from 2004 to 2020 were used. The data can be found in https://data-seattlecitygis.opendata.arcgis.com/. The description of each feature (metadata) can be found here https://www.seattle.gov/Documents/Departments/SDOT/GIS/Collisions_OD.pdf
The source contains a mixture of categorical and continuous variables.

### Feature selection

The entire set was immediately divided into a train and test set. Decisions on feature dropping and data cleaning were taken by analyzing only the test set and applied to both the train and test sets. This choice was motivated to avoid lookahead bias. Figure 1 shows a summary of the test set.

Table 1 summarizes the decisions taken for selecting the features.

First, the features identical to the target or containing the same type of information were eliminated, for preventing the models to use the target as a feature.

Second, some features are missing an appropriate description and have a large percentage of missing values. These features were dropped.

Third, features containing administrative information or collision details available only after a collision occurred were eliminated. 

Fourth, the INCDATE feature was eliminated as contains the collision date as in the INCDTTM feature. 

Fifth, the location feature was eliminated because it contains information that is similar to the X and Y GPS coordinates.