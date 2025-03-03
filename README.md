
# Chicago Car Accidents Analysis

Justin Lee

This notebook is prepared for the Vehicle Safety Board of Chicago. This board aims to understand how often driver accidents are caused by a failure to yield. The purpose of this analysis is to build a model that accurately predicts how often failure to yield accidents are actually due to a failure to yield.

## Data Understanding
This dataset is from the Chicago Data Portal. This data contains information about people involved in a crash and if any injuries were sustained. Each record corresponds to an occupant in a vehicle listed in the Crash dataset. Some people involved in a crash may not have been an occupant in a motor vehicle, but may have been a pedestrian, bicyclist, or using another non-motor vehicle mode of transportation. Person data can be linked with the Crash and Vehicle dataset using the “CRASH_RECORD_ID” field.
## Data Preparation
In order to prepare our analysis, we must prepare our data into binary classification. First we'll drop any unnecessary columns for our analylsis. These below columns will get dropped because they will not actually help us with our analysis. We'll handle the NONE, OTHER and UNKNOWN values to be set to null. Then we will create a new binary column where 1 represents FAILED TO YIELD and 0 represents all other driver actions. Finally, we will one-hot encode our categorical columns into numerical ones.
##  Baseline Model & Evaluation
Logisic regression is a good baseline model because it is simple, interpretable and efficient to provide a strong foundation for comparison. This will help us determine what improvements will be needed when iterating on our model. We'll first impute our null values to fill with mode values so that we can maintain the integrity/size of our data. Then we will scale our features because logistic regression is sensitive to feature magnitudes.

Our model achieved 93% accuracy which looks great but this class is also very imbalanced.

Our model had 372,149 true negatives which is the total correctly predicated "NOT FAILED TO YIELD". We had 3 false positives which are the incorrectly predicted "FAILED TO YIELD" cases but were actually "NOT FAILED TO YIELD". Our model missed 29,018 actual "FAILED TO YIELD" cases. Our model only correctly predicted 6 "FAILED TO YIELD" cases.

Precision for Class 1 ("FAILED TO YIELD") was 0.67 but this is misleading due to only having 6 true positives. We also had a recall score of 0 and f1-score of 0. A recall of 0 means that our model is not detecting actual "FAILED TO YIELD" cases at all. Out of 29,024 actual "FAILED TO YIELD" cases it only found 6. This means it failes to identify accidents caused by "FAILED TO YIELD". Since our recall is 0, it makes sense that our f1-score is also 0. Our baseline model is completely missing the "FAILED TO YIELD" category.

Our baseline model is heavily influenced by class imbalance. We are seeing 372,152 "NOT FAILED TO YIELD" cases versus only 29,024 "FAILED TO YIELD" cases. Since "NOT FAILED TO YIELD" dominates the data, the model learns to always predict the majority class (0) because it minimizes overall errors.

Overall our model is not detecting meaningful relationships between features and "FAILED TO YIELD". Pseudo R-squared compares the log-likelihood of our model versus a null model (a model with no predictors) so a lower score suggests a model doesn't explain much variation. Our pseudo R-squared value of 0.07781 means our model doesn't explain much variation in the outcome. Logistic regression maximizes the log-likelihood to find the best-fit model, so a higher value suggests a better fit. Our log-likelihood means of -3.8426e+05 suggests a week model, which could be due to a large class imbalance making our model predict mostly 0s - inflating accuracy but weakening predictive power. All p-values are high, meaning features may not be relevant predictors. This model also did not converge which is likely due to our high class imbalance.
## Resampled Model & Evaluation
We need to balance the dataset. As we saw earlier our target column had one class's value almost be 10x the amount of values of the other class. For this reason we will balance the dataset using an under sampling technique. Under sampling removes excess majority class samples and ensures the model learns from real data only, it helps the model focus to learn from minority class cases instead of predicting "NOT FAILED TO YIELD" cases, and it would help us increase our F1-score as we strive for a healthy balance between recall and precision.

Our accuracy dropped from 92.76% to 29.73%. This drop is expected due to balancing the dataset.

Our precision from our baseline model to our resampled balanced model went from 0.67 to 0.09, meaning we have more false positives now. Our recall jumped from 0 to 0.99 and our F1-score also increased from 0 to 0.17.

We have 90,673 true negatives, meaning these are the cases our model correctly predicted "NOT FAILED TO YIELD". We have 281,479 false positives, meaning our model incorrectly predicted "FAILED TO YIELD" when it was actually "NOT FAILED TO YIELD". We have 398 false negatives, meaning our model incorrectly predicted "NOT FAILED TO YIELD" when it was actually "FAILED TO YIELD". And we have 28,626 true positives, meaning these are all the cases that were correctly predicted as "FAILED TO YIELD".

Our decision tree model performed very similarly to our resampled model. The decision tree model's accuracy is 29.23% and the undersampled model's is 29.73%. The precision, recall and f1-score all remained the same as our undersampled model. Our false positive count from our undersampled model to our decision tree model jumped from 281,479 to 283,606. This model did not improve significantly from our logistic regression.
## Conclusion
The Vehicle Safety Board of Chicago is likely focused on identifying as many "FAILED TO YIELD" caes as possible (high recall) and minimizing incorrect classifications of "FAILED TO YIELD" (high precision), therefore having a nice balance between the two metrics. This would mean our F1-score is the strongest metric our board cares about.

After testing multiple models, our resampled logistic regression model remains the best choice for the Vehicle Safety Board of Chicago. This model increased F1-score from 0 (baseline) to 0.17, showing a significant improvement in balancing recall and precision. Our baseline model was useless for safety analysis, as its recall was 0, meaning it completely ignored all "FAILED TO YIELD" cases. The board cannot make policy recommendations if the model fails to detect real incidents.

We also tested a Decision Tree Classifier to see if it could provide a better balance. While the Decision Tree maintained a high recall (0.99), it did not improve precision or F1-score, producing results nearly identical to the undersampling logistic regression model. Additionally, the Decision Tree had slightly more false positives, which further confirms logistic regression with undersampling remains the best option.

Moving forward, improving precision without sacrificing recall should be the focus of further iterations.
## Next Steps
The Vehicle Safety Board of Chicago should incorporate GIS (Geographic Information System) data to examind the spatial distribution of the accidents. By analyzing latitude and longitude coordinates we can identify high-risk locations where "FAILED TO YIELD" accidents frequently occur. Using spatial clustering or heat map analyses we can rank locations based on accident frequency. After finding out these high traffic areas, we could assess traffic flow patterns, road design issues and driver behavior.

From these results we could make evidence-based solutions to decreasing frequency of these accidents. By integrating GIS data into our machine learning model, we can strengthen policy recommendations and develop targeted interventions.
