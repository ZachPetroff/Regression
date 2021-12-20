# Regression
Regression with Sklearn

# Data Preprocessing

The first step of data preprocessing I took was deleting the columns I knew I did not
need. The “id” column simply is used as an index and does not provide any additional
information, so that column was dropped. The “dropoff_datetime” was not included in the test
data (because having the pickup and drop off times would make this a really easy problem), so
that column was also dropped.Next, I checked for any missing values. Luckily, the data had no
missing values, so I did not have to impute or remove any additional data.
Then, I had to deal with the datetime values contained within the “pickup_datetime”
column. I had never dealt with datetime values before, however, I knew if I left them as is, the
model would likely treat them as categorical variables, with each time being a different category.
This would be bad, because time is continuous. Instead of trying to reform the data in a way to
let the model know that it is continuous, I just reduced the number of categories. I did this by
binning the times according to the season (winter, spring, summer, fall) and time of day
(morning, midday, night). I also encoded the columns “vendor_id” and “store_and_fwd_flag” to
be binary (0 or 1).
The final step of data preprocessing was to determine which features were actually
adding information and which features could be dropped. Usually, I would forgo this step, but
there was a lot of data, so I was concerned about runtime. I used the random forest algorithm to
rank feature importances, and determine which features were not adding any additional
information. I determined the ‘store_and_fwd_flag’, ‘fall’, ‘winter’, and ‘morning’ columns could
be dropped. The ‘fall’, ‘winter’, and ‘morning’ columns make sense to drop, because the other
corresponding season and time of day columns probably contain enough information to make
these columns redundant.
I also tried to change the longitude and latitude to distance values by taking the
euclidean distance. I thought this might have a strong linear relationship with trip duration,
however I was incorrect and there was very little correlation, so I just left the longitude and
latitude values as is.

# Model building

I tried many different models, however, it was very difficult to find a good model (an
R-squared value above .7). All the models were scored using cross-validation, and compared
using the R-squared metric. Initially, I tried random forest regression, nearest neighbors
regression, elastic net, ridge regression, and lasso regression. The only model that scored
above .01 was elastic net. Next I tried using a linear SVM, paired with Nystreom transformation.
This model, too, performed very poorly, although better than the elastic net model. I was getting
a bit hopeless at this point. Finally, I tried a regular SVM. However, because this model takes
awhile to run, I only used a random sample of 20,000 pieces of data. With only 20,000 pieces of
data, this model performed much better than the other models. However, it still had a very low
R-squared score (around .03).
My plan then became to tune the hyperparameters for one SVM with minimal data, then
to scale up the amount of data. I found the best value of C to be 100 and the best value of
epsilon to be .001 with 20,000 pieces of data. I then performed cross-validation with 400,000
pieces of data. This turned out to be a bad plan, though, because different amounts of data
affect how much regularization the model requires. Thus, the model with 400,000 pieces of data
performed much worse. My plan then became to train 10 SVR models with 20,000 pieces of
data, then combine the prediction using a sklearn’s voting regressor. This plan also did not go
well, and the model ended up performing very poorly. Finally, I decided to train 20 SVR models
on 20,000 different random samples of data, then to take the mean. I was able to get a final
score of 
