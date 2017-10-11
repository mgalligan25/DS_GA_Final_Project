# Predicting Scientist Migration to the U.S.

In this project, I will predict the liklihood of foreign scientist migration to the United States using a subset of ORCID data provided through [ORCID](https://orcid.org/content/download-file "ORCID Download File") and [Kaggle](https://www.kaggle.com/jboysen/scientist-migrations "Kaggle Scientist Migration Page"). Specifically, the goal is to identify the foreign scientists' likely to migrate to the U.S. given the earliest information provided through ORCID. To the extent there is error, the model will be designed to err to the side of false-positives so as not to miss migrants, i.e. the model will emphasize recall over precision.
### Acknowledgements
Bohannon J, Doran K (2017) Introducing ORCID. Science 356(6339) 691-692. http://dx.doi.org/10.1126/science.356.6339.691

The Dryad data package:
Bohannon J, Doran K (2017) Data from: Introducing ORCID. Dryad Digital Repository. http://dx.doi.org/10.5061/dryad.48s16


### Dataset Stats
```
Dataset Shape: 263762 rows, 9 columns
Primary Key: orcid_id
Columns: earliest_year, earliest_country, end_year, Ringgold_id, affiliation_type, affiliation_role, has_phd,phd_year, US_Migrant
Slightly less than half of foreign ORCID scientists are PhDs
Only 3% of foreign ORCID scientists are U.S. Migrants
Average year of entry: 2000
Average year of exit: 2006
Over 76,000 unique affiliation roles from scientists all over the world in many different languages

```

## Implement Preprocessing Functions
The first thing to do to any dataset is preprocessing. I'll implement the following preprocessing functions below:

+ Feature Imputation
+ One Hot Encoding

## Feature Imputation
The following features were added to the dataset:
+ **phd_age**: (phd_year - earliest_year) 
+ **life_science**: boolean flag for whether life science root words appeared in the affiliation_role feature
+ **physical_science**: boolean flag for whether physical science root words appeared in the affiliation_role feature
+ **social_science**: boolean flag for whether social science root words appeared in the affiliation_role feature
+ **engineering**: boolean flag for whether engineering root words appeared in the affiliation_role feature
+ **humanities**: boolean flag for whether humanities root words appeared in the affiliation_role feature
+ **education**: boolean flag for whether education root words appeared in the affiliation_role feature
+ **other**: boolean flag if no root words in earlier fields could be identified in the affiliation_role feature

## One Hot Encoding
All categorical features (e.g. earliest_country) were encoded numerically without heirarchy. Ringgold_id (organization ID) was dropped for too many null values. For specifics on any other null value cases, please see Python notebook.

## Train
```
Only 3% of foreign ORCID scientists in this dataset are US Migrants so I used the SMOTE algorithm through imblearn to upsample US Migrants for training.

Using this synthetically upsampled training data, I tuned 3 classifiers with various strengths and weaknesses. 
Then I used a soft voting classifier (weighted according to recall performance against the raw training split) to create an ensemble model. 
Different weights produced slightly higher recall in validation (as high as .87 recall), but at too great a cost to overall accuracy (just above .6).

Classifiers used (in order of voting weight):
XGBClassifier
GradientBoostingClassifier
kNeighborsClassifier

Average model performance across 10 folds
Upsample training dataset
recall: .917

Validation:
accuracy: .685
recall: .814

```

## Test
```
Test scores are very similar to validation, as validation is just the raw training data (without upsample). 

Test Results
accuracy: .691
recall: .807
```