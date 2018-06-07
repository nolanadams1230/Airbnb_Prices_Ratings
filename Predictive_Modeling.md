# Predictive Modeling

[Jupyter Notebook](http://nbviewer.jupyter.org/github/nolanadams1230/Airbnb_Prices_Ratings/blob/master/notebooks/Predictive%20Modeling.ipynb)

[Github](https://github.com/nolanadams1230/Airbnb_Prices_Ratings/blob/master/notebooks/Predictive%20Modeling.ipynb)


#### Created Dummy Variables
Starting out I created dummy variables, while dropping the first column for all the categorical features. We dropped the first one because one level of the categorical feature becomes the reference group during dummy encoding for regression and is redundant. 

#### Train/Test Split
Next, I created different splits of my data for both *ratings* and *price*. This way I can include rating as a factor for price & vice-versa. I split the data into 70% training set and 30% for testing. 

#### Standarize the Features
I used the standard scaler from sklearn to standardize the training and test sets. Using variables without standardization can give the variables with larger ranges greater importance in the analysis. Transforming the data to comparable scales can prevent this problem. We only need to worry about this for models that use some form of a distance formula (Ridge, SVR, etc.) while others don't (Decision Tress, Random Forest, etc.). Since, it is not necessary to standardize our features for all models I created them as new variables of the train/test sets.

#### Custom Function - Reg_Model
I created a custom function that can either be used to perform cross-validation or perform a gridsearch for finding the optimal hyper-parameters (gridsearch=True). 
* Cross-Valdiation: This allows me to plug in any estimator with scaled or unscaled data, split the training data into 5 splits, and obtain 5 validation scores. 
  * It *prints* out the 5 r-squared validation scores, 5 training scores, the average r-squared validation score, the average train score, and the average MSE.
  * It *returns* the model with the training set fit to it for if we wish to make predictions and also the cross-validation model.
* Grid-Search: Allows me to add a range of values for whatever hyper-parameters I wish to test with the grid search.
  * It *prints* the average r-squared validation score, average test score, the best estimator and the best parameters.
  * It *returns* the gridsearch model

#### Custom Function - SFM
I created a function to plug in a model then perform feature selection with sklearn's SelectFromModel. This removes all features below a set threshold, and is great getting rid of weak features that may be hindering our model's performance.
* The function *prints* the average r-squared validation score, the average test score, the average mean squared error, and the new shape of the training set.
* It *returns* the new X train and X test sets, so we can use it in the gridsearch / final model if it performs better than the model without it. It returns the SelectFromModel meta-transformer also.


#### Applying Fixes From EDA
Regression can be get thrown off very easily by extreme values, and this dataset has a quite a few extreme values. In our Exploratory Data Analysis we made serveral observations of outliers and multicollinear features that we will remove before we continue. 
* Removing observations with *extreme outliers.*:
  * We removed all observations that had a maximum nights value above 1125 days. It is un reasonable to image anyone renting for that long, but over 44% of listings had a value of 1125 for their maximum night. My guess is this is the maximum value possible and the 13 observations over this were due to some error.
  * We removed all observations that has a minimum night value above 60 days because even if these were not errors, this is a very extreme case of listings that only represent .0001% of listings in San Francisco. It would be better to remove these, to prevent them from hurting our model.
  * We removed all listings with a price greater than $1000 a night. These are also extreme cases that do not represent the overwelming majority of the listings in San Francisco and these really skew our data.
* Removed some *multicollinear features*:
 * We chose to remove one of any pairs of features with a correlation of .75 or higher. I felt that these features were well represented by the other pair if the correlation was that high. Since, removing features does not always improve a model I saved the new training set to its own variables (X2_train and X2_test), so that we can try both the datasets on each estimator, then use gridsearch the best scoring one.

# Price
## Modeling

## Baseline Estimator
Running a linear regression model we obtained a baseline of -2.684e+17. Then applying the SFM custom function on the X2 features we impoved the validation r-squared score 0.0674 with a standard deviation of 0.0165. This is great improvement, but still poor results. This will be our new baseline for comparing the rest of the models.

## Results
For each model we used, we trained the model with the data from both the dataset with all the features and the dataset with the features / outliers we removed. Next, we performed our feature Selection on both models and then took the best scoring model from the four possibilities and then tuned the hyper-parameters for that chosen model. These were the results for each model:
* Ridge:
  * Average Validation Score: 0.6361
  * Validation Standard Deviation: 0.0361
  * Average Train Score: 0.6684
* Lasso:
  * Average Validation Score:0.6267
  * Validation Standard Deviation:0.0404
  * Average Test Score: 0.6747
* ElasticNet Score:
  * Average Validation Score: 0.6344
  * Validation Standard Deviation:0.0380
  * Average Train Score:0.6679
* Support Vector Regression:
  * Average Validation Score: 0.5864
  * Validation Standard Deviation: 0.0462
  * Average Train Score: 0.6442
* K-Nearest Neighbor Regression:
  * Average Validation Score: 0.3355
  * Validation Standard Deviation: 0.0133
  * Average Train Score: 0.4087
* Decision Tree:
  * Average Validation Score: 0.5487
  * Validation Standard Deviation: 0.0232
  * Average Train Score: 0.6450
* Random Forest:
  * Average Validation Score:  0.6161
  * Validation Standard Deviation: 0.0513
  * Average Train Score: 0.8873
* Extra Random Forest:
  * Average Validation Score: 0.6344
  * Validation Standard Deviation:0.0441
  * Average Train Score:0.9479
* AdaBoost:
  * Average Validation Score: 0.6076
  * Validation Standard Deviation:0.0388
  * Average Train Score:0.8090
* Gradient Boosting:
  * Average Validation Score: 0.6606
  * Validation Standard Deviation:0.0433
  * Average Train Score:0.8584
* eXtreme Gradient Boosting:
  * Average Validation Score: 0.6516
  * Validation Standard Deviation:0.0444
  * Average Train Score: 0.8929

The **3 best models** are:
* **Gradient Boosting:** It has the best validation r-squared score with relatively low variation.
* **eXtreme Gradient Boosting:** It has the second best validation r-squared score also with relatively low variation.
* **Ridge Regression:** Even though it has the 5 best validation r-squared score, it has a lower variation then the third and fourth best scoring models. 


We tested are three chosen models on the unseen data (Test set) and the best model was: **eXtreme Gradient Boosting** model.
* The **eXtreme Gradient Boosting** model is our choice with having a Test Score of: *0.6755*. This model explains around 68% of the variation in the model.

Looking at the feature's coefficients and importances, we get a better understanding of how the features impact the price for a listing in San Francisco.
* Renting an Entire home/apt significantly impact the price
* The marina neighborhood is the best area to rent out an airbnb to increase revenue.
* Providing / having amenities such as a fireplace, air conditioning, patio or balcony, shampoo, or dishwasher bring in higher revenue per day

# Ratings

## Results
The results for each model:
* Ridge:
 * Validation Score:0.7295
 * Standard Deviation: 0.0627
 * Average Train Score: 0.7762
* Lasso:
 * Validation Score:0.7257
 * Standard Deviation: 0.06076
 * Average Train Score: 0.7633
* ElasticNet:
 * Validation Score:0.7247
 * Standard Deviation: 0.0597
 * Average Train Score: 0.7629
* Support Vector Regression: 
 * Validation Score: 0.6980
 * Standard Deviation: 0.0580
 * Average Train Score: 0.7523
* K-Nearest Neighbor Regression:
 * Validation Score: 0.3409
 * Standard Deviation: 0.0595
 * Average Train Score: 0.4549
* Decision Tree:
 * Validation Score: 0.6118
 * Standard Deviation: 0.0990
 * Average Train Score: 0.7306833453851157
* Random Forest: 
 * Validation Score: 0.7162800025374415
 * Standard Deviation: 0.041197528534237304
 * Average Train Score: 0.9365517874644289
* Extra Random Forest: 
 * Validation Score: 0.7266410632341096
 * Standard Deviation: 0.050464765333707605
 * Average Train Score: 0.8699126269869307
* AdaBoost: 
 * Validation Score: 0.6890654778698304
 * Standard Deviation: 0.05416624127036092
 * Average Train Score: 0.8124426792259145
* Gradient Boosting: 
 * Validation Score: 0.7242749414643672
 * Standard Deviation: 0.05218437349866777
 * Average Train Score: 0.8620552524283013
* eXtreme Gradient Boosting
 * Validation Score: 0.7296639299171241
 * Standard Deviation: 0.06342659674274964
 * Average Train Score: 0.9083146190565304

The top models are:

Extra-Random Forest: It has the third largest validation score, but a much lower standard deviation the top scoring model.
eXtreme Gradient Boosting: It is the top validation score
ElasticNet Regression: It has much less variation the the top scores that are right around its score.
We can compare these three models against that actual Test data in order to choose our model.

Model Selection
The eXtreme Gradient Boosting model is our choice for predicting ratings, with having a Test Score of: 0.7305. This model explains around 73% of the variation in the model.

Looking at the feature's importances and the features' coefficeients to get a better understand of how features impact the ratings for a listing in San Francisco. Some of the interesting insights about the features that are:
* The accuracy of the listing posting the most important factor. Next is how the cleanliness. Host should focus on these two factors if they want to have the largest postive impact on their ratings.
* No amenities had strong importances on the random forest, yet there was a lot that had high coefficients from ridge regression.
* Location / Neighborhood does not have a strong affect on ratings.
