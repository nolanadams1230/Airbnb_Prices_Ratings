# Data Wrangling

[Jupyter Notebook](http://nbviewer.jupyter.org/github/nolanadams1230/Airbnb_Prices_Ratings/blob/master/notebooks/Data%20Wrangling.ipynb)

[Github](https://github.com/nolanadams1230/Airbnb_Prices_Ratings/blob/master/notebooks/Data%20Wrangling.ipynb)

## The Data - Inside AirBnB

The data was obtained from independent, non-commercial organization that utilizes public information compiled from the Airbnb web-site and is available to download on http://insideairbnb.com/get-the-data.html. The original dataset is a static 365-day snapshot from October 2, 2017 and includes 96 features on 8933 listings in San Francisco. These 96 features include 6 date/time features, 33 numerical features, 32 categorical features, 21 textual features, and 4 id keys.
 
# Process

We first started out removing the unneeded columns:

* **Unused Keys/IDs:**: 
  * Unused keys:(last_scraped, scrape_id)
* **URLs:** 
  * Links to pictures or the listings that will not be used in the analysis: listing_url, thumbnail_url, medium_url, picture_url, xl_picture_url, host_url, host_thumbnail_url, host_picture_url
* **Redundant Information:**
  * Since our analysis takes places in San Francisco, CA the following features all have the same value for each listings: city, state, market, smart_location, country_code, country, jurisdiction_names
  * In our analysis we use the features "zipcode" and "neighbourhood_cleansed" for understanding the impact location has on *Price* and *Ratings*. We don't need the following features, since the previously mentioned two sufficently capture the information needed: latitude, longitude, neighbourhood
   * is_location_exact refers to if the longitude and latitiude are exact or within a mile radius of the listings true location. Since we are not using latitude and longitude in our analysis we can drop this one as well.
  * calculated_host_listings_count, host_total_listings_count, and host_listings_count all have the same values, so we chose to just keep calculated_host_listings_count for our analysis 
* **Irrelevant Information About Host:**
  * These are features are text columns that most likely wouldn't have a strong impact on the model if we were to attempt to transform them: license, host_name
   * I doubt that a customer bases their ratings or how much they are willing to pay off of a host's location or their verifications, so we can drop: host_location, host_neighbourhood, and host_verifications
* **Calendar Information:** 
  *  The calendar is not very accurate, because it cannot tell the difference between a listing where a host has blacked out their own dates because they aren't renting or a listing with its dates blacked our becuase it has already been booked by customers. A lot of host will black out dates until the time is closer, so the availability metrics don't necessarily represent how many days they have left to rent: availability_30, availability_60, availability_90, availability_365
* or Any column with 1 or less unique values

From there we had 56 columns remaining. These included our Response Variables (Price & Ratings), 24 Numerical columns, 11 Text columns, 16 categorical columns, and 4 date/time columns.

**Numerical**: Of the 24 numerical columns (26 with the response), all were loaded in as a string, and 7 of those were loaded in with special characters (Currency or Percentage). We used a loop to replace these special characters with an empty string, and then converted all 26 to a float.
* Now that the data has been transformed into a numerical type, we can find and remove any listings that are no longer active. The reason to get rid of these listing is that our goal is to find ways to increase the ratings and price of listings and if a listing is inactive then they cannot benefit from our analysis anyway. They are not a good representation of the population we are trying to help, so they may negatively affect our predictive performance. Using the column *'number_of_reviews'* we removed any rows that had less than 1 more review.
* Next we looked at the Null Values for each numerical column, so that we could either impute the missing values, transform the feature into something else, or know if we needed to remove it:
 * **Square Feet**: The square feet column has over 97% of its rows missing. Instead of just removing the column, we transformed it to a column that states whether a listings has included square feet. 
 * **Weekly & Monthly Price**: These columns have a high amount of missing values. These columns represent a seperate price (A discount) if you are to book by the over a Week / Month. We transformed these to binary values that say if there is a discount or not for a longer stay
 * **Bedrooms / Bathrooms / Beds**: Since nthe column accommodates had no missing value and is highly correlated with all these columns, we imputed the missing values based on the median value of the respective columns that matched with the value for accommodates.
 * **Review Scores**: review_scores_rating (One of our response variables) had no missing value, so we can use that to impute the missing values for the other review scores, since they are all highly correlated with ratings
 * **Security Deposit / Cleaning Fee**: Security Deposit is most correlated with bedrooms(.32) and price(.32) and Cleaning Fee is most correlated with accommodates(.56), bedrooms(.59), beds(.56), and price(.51). We imputed cleaning fee first with medain value grouped by bedrooms bedrooms, so that we could impute the security deposit with the cleaning fee.
 * **Host Response Rate**: We removed this column, because there was extremely small variation in the listings. 95% of listings had 100% response rate.
 
 **Date/Time Columns**: We turned all four columns to the numerical type, so we can add them to our model. The data was scrapped on October 2, 2017, so all dates are relative to this date. We used the datetime.datetime and datetime.timedelta package to convert each column into days since the time scrapped.
 
 **Text Columns**: Since we can't impute for missing values in text columns, we just filled missing values with blanks ''. Then we calcualted the average of the length of all the text columns, because there may be a relationship between the effort put into the listing summary, description, etc. with the ratings or price. We kept the length of the column *house_rules* and deleted the rest. The reason we kept this column is because if the list of rules is too long, then that may have a negative relationship with reviews. No one wants to have to worry the whole time about a bunch of ruless while on vacation.
 
 **Categorical Columns**: Our categorical data contains Binary columns, Multi-level columns, and a list within each row. We need to turn our categorical data from text into the categorical or boolean type.
 * **Amenities:** The amenities column contains a list of all the amenities that a listing has. We then created dummy variables without removing the first column, since not having all the other's doesn't imply you would have the removed amentity. In total there were 124 different amentities.
 * **Binary Categorical Columns**: These columns were all *True/False* columns, but loaded in as 't' or 'f'. We converted the text to 'True' or 'False', and then transformed it into boolean type.
 * **Multi-Level Categorical Columns**: We changed these to categorical type and then checked from null values. Zipcode and Host_response_time were the only ones to contain null values:
  * **Host Response Time**: There were no easy ways to impute this column, so we used a niave method of backfilling the missing values.
  * **Zipcode**: The neighbourhood_cleaned column had no missing values, so we created a dictionary of each neighborhoods most frequent value and then imputed the missing values by matching the dictionary neighborhood and its most frequent zipcode 


We created two list of features for each response variables, since we will be performing the rating regression analysis without the review scores, but keeping them for the price regression analysis.

**Multicolinearity**: Multicollinearity of predictor variables can be harmful for some regression models, so we created a function to recursively calculated the *Variance Inflation Factor (VIF)* for each variable and remove the variable with the highest VIF score and then repeatedly recalculated the VIF and remove variables until no columns contained a VIF score of 5 or more.
* The [variance inflation factor (VIF)](https://en.wikipedia.org/wiki/Variance_inflation_factor) is the ratio of variance in a model with multiple terms, divided by the variance of a model with one term alone. It quantifies the severity of multicollinearity in an ordinary least squares regression analysis. Any value higher than 5 is a variable we should consider investigating, and in any variable with a VIF above 10 in considered extremely multicollinear and should be removed.
 * The features with a VIF score between 5 and 10 are up to us if we want to remove them. Since the VIF scores were pretty similar for both PRICE and RATINGS features, we should keep the following columns for both:
  * **Beds:** We need a variable that can accurately represent the size of the listing and since bedrooms, accommodates, and bathrooms are already removed we should keep this sinceit has the lowest VIF score
  * **Cleaning Fee:** This may give insights, into if choosing to charge more for cleaning negatively affects the price.
  * **Days since Host:** The relationship with how long a host has been renting an airbnb and price is one we should keep.
  * **Hair Dryer & Shampoo:** These amenities are ones that are not usually supplied by the host. This may give insights in if it is worth spending a few extra dollars on amenities like these.
 

# Final
After finishing cleaning the data the listings.csv contains 7017 rows and 178 columns with no missing values. We will remove the *multicollinear features* before we model, so that we can compare models with and without the multicollinear features

