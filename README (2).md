
# Chicago Car Accidents Analysis

Justin Lee

This notebook is prepared for the Vehicle Safety Board of Chicago. This Board aims to understand the primary contributory causes of a car accident in order to reduce traffic accidents.

## Data Understanding
This dataset is from the Chicago Data Portal. This data contains information about people involved in a crash and if any injuries were sustained. Each record corresponds to an occupant in a vehicle listed in the Crash dataset. Some people involved in a crash may not have been an occupant in a motor vehicle, but may have been a pedestrian, bicyclist, or using another non-motor vehicle mode of transportation. Person data can be linked with the Crash and Vehicle dataset using the “CRASH_RECORD_ID” field.
## Data Preparation
In order to prepare our data for analysis we must drop any unnecessary columns, fill any NA values, scale our numerical features, and encode our categorical features.

Because my audience is a Vehicle Safety Board they are likely more interested in driver behavior and physical condition rather than specific pedestrain details.

For that reason, our best target column candidates are driver_action, physical_condition, and cell_phone_use. Let's explore the data for each candidate to make an informed choice.

DRIVER_ACTION will be our target variable because it directly describes the driver behavior leading to crashes, has specific categories that align with real-world accident causes, and has more predictive power than other features.
##  Baseline Model & Evaluation
We will use a Decision Tree Classifier as our baseline model because our DRIVER_ACTION target is a multi-class target and it is easily interpretable for an initial benchmark for performance.

Our Decision Tree classifier correctly predicts the primary contributory cause in about 78% of cases.

Class 0 has a high recall of 97% and F1-score of 88%, meaning our model heavily favors this class. Classes -2 and -1 have low recall scores meaning they are rarely predicted correctly. Most predictions fall into class 0 (making up 320,700 cases) whereas many instances of class -2 and -1 are misclassified as 0. This suggests class imbalance.

Because our baseline model has a biased towards the majority class of 0, our iterated model will address handling class imbalance.
## Balanced Model & Evaluation
Due to the large majority class bias we saw in our baseline model, we will use sklearn's classifier to alow class_weight = 'balanced', which automatically assigns higher weight to minority class.

Our balanced model has some improvements compared to our baseline model. Our balanced model now recognizes minority classes -2 and -1 much better, the recall for class -1 increased from 2% to 61%, and we overall now have more balanced predictions rather than predicting class 0 for everything.

We do also see more problems with our balanced model. Our accuracy dropped significantly from 78% to 49%, likely because our model now misclassifies a lot of the 0 cases. Our 0 class also dropped in recall from 97% to 50%, meaning it no longer prioritizes the dominant class. Lastly, our precision is low for minority classes (14%-18%) which could mean that this is incorrectly predicting -2 and -1.
## Conclusion
After testing two models, a baseline Decision Tree and a balanced Decision Tree using class weights, we can see a distinct trade-off between overall accuracy and fairness in class representation.

Our baseline model had high accuracy but was biased. This baseline model performs well overall but heavily factors the majority class of 0. This means that our model is not useful for identifying less common accident causes because it misclassifies them most of the time.

Our balanced iterated model is fairer but has worse accuracy. Our model improved the recall score for both the minority classes (-2 and -1) but the accuracy dropped from 78% to 49%.

Our averaged F1-score increased from 0.33 to 0.37. This suggests that our balanced model is slightly better at handling all classes.

Because our audience is the Vehicle Safety Board, they might care about idetnifying the primary cause of accidents, especially minority causes, to develop better safety policies and prevention strategies. The Vehicle Safety Board might also care more about balanced performance across all causes.

For a Vehicle Safety Board, the best approach is to prioritize recall (how well our model identifies actual cases of a class) and fairness over pure accuracy. The balanced Decision Tree is a better choice for this business problem than the baseline model.
## Limitations & Next Steps
There are a few next steps we could go with our model.

We can improve upon our model's performance. A next iterated model we could explore is the Random Forest model, which handles imbalanced data better than a single Decision Tree and reduces overfitting.

We could consider external factors to add into our data. Weather data, traffic density, and road conditions would all be great factors to add to our data to help improve model accuracy by adding environmental context.

We could create policy recommendations based on our model results. For example, if impaired driving is a leading cause of crashes we could enforce stricter DUI regulations, if road conditions played a major role then we could advocate for better road lighting, if cell phone usage was a major factor we could increase penalties for distracted driving.