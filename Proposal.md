# AirBnB’s San Francisco Listings

## Problem
With over 4 million listings in 191 different countries, Airbnb has become an amazing investment opportunity for people all around the world. While it is a remarkable achievement, the supply side continues to grow, which can make being an Airbnb host difficult. What can a host do to allow their listing to stand out and ultimately increase their revenue? This analysis aims to understand the most important factors for predicting  the prices and ratings for every listing in San Francisco.

## Client
Airbnb would benefit from using this analysis by being able to advise their hosts on the most important factors that lead to higher ratings and higher prices. By understanding the most important factors for price, a host could optimize their listing to increase their revenue. By understanding the factors for ratings, AirBnB’s host would be able to create a better experience for Airbnb customers, who will be more willing to use the service again, while fostering positive communication around booking with AirBnB.

## Data
The data was obtained from independent, non-commercial organization that utilizes public information compiled from the Airbnb web-site and is available to download on http://insideairbnb.com/get-the-data.html. The original dataset is a static 365-day snapshot from October 2, 2017 and includes 96 features on 8933 listings in San Francisco. These 96 features include 6 date/time features, 33 numerical features, 32 categorical features, 21 textual features, and 4 id keys.

## Approach

I will start my approach by cleaning the dataset. I will remove or transform any uneeded, redudant, irrelevant, or bad features from the dataset, so we can isolate the features that will be used in our model. From there we will explore the features and create new features that could help the model. Once I have completed the "Data Wrangling" stage I will explore the the features and their relationships with the two response variables. This will allow us to find outliers and/or multicollinearity among the features. From there we will seperate our data into a training set and test set, so that we can determine the best model to predict price and ratings. We will perform k-folds cross-validation on the training set for each model, in order to compare models and tune hyper-parameters with the average r-squared from the validation sets. From there I will create a write-up and slide deck on my analysis for AirBnB and also keep relevant jupyter notebooks containing detailed code, outputs and visualizations.

## Deliverables

1. **Code** - Jupyter Notebooks of the Data Wrangling, Exploratory Analysis, and Predictive Modeling
2. **Written Report of Findings** - A write up that explains each step of the process and the results.
3. **Summary Presentation** - A slide deck that can be used for a presentation of the analysis







