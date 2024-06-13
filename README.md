# The Recipe to a Good Recipe

Author: Sharon Ni (s3ni@ucsd.edu)

This project is a comprehensive analysis that examines the features associated with online recipe projects. It features an analysis spanning the entire data science lifecycle, from data cleaning and exploratory data analysis to hypothesis testing and model building. This project is part of the coursework for DSC80: Practice and Application of Data Science at UCSD. 

## Introduction:
Cooking is a popular hobby for many, offering not only the joy of creating delicious meals but also the satisfaction of sharing these creations with friends and family. With the expanding reach of online platforms, sharing recipes has become much easier and more widespread. Specifically, through the popular website food.com, users can readily access recipes, rate them, and interact with fellow enthusiasts who share a similar hobby. 

Following this growth, an intriguing and pertinent question emerges: __what factors correlate with a highly-rated recipe?__ As users engage with recipes online, they are exposed to a variety of valuable information but also the occasional long backstories that seem to delve into the contributor's entire nostalgic childhood memory associated with the food. While some may appreciate the heartfelt, lengthy introductions, others may simply be seeking for concise directions. My analysis aims to examine the relationship between recipe length—consisting of factors such as number of steps, length of directions, and length of descriptions—and recipe ratings to uncover insights into user preferences when interacting with recipes online. This understanding can help recipe creators enhance their content to better align with user preferences and ultimately improve the overall cooking and sharing experience. 

My analysis specifically focuses on the popular recipe-sharing website, food.com. The two datasets below are extracted from this site, containing recipes and ratings shared since 2008. 

### Data

The first dataset, titled `recipe`, contains recipes from the [food.com](https://www.food.com/). It is a subset of the 
original data, containing only recipes posted after 2008. The dataset has 83,782 observations and 12 variables. A short
description of each variable is provided below. Variables relevant to my analysis include `name`, `n_steps`, `steps`,
and `description`.

| variable | description |
|:----------|:-------------|
| name     | recipe name (str) |
| id       | recipe id (int) |
| minutes | minutes to prepare recipe (int) |
| contributor_id | user id who submitted this recipe (int) |
| submitted | date recipe was submitted (str) |
| tags | food.com tags for recipe (str) |
| nutrition | nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value" (str) |
| n_steps | number of steps in recipe (int) |
| steps | text for recipe steps, in order (str) |
| description | user-provided description (str) |

The second dataset, titled `interactions`, contains ratings and reviews of recipes from the [food.com](https://www.food.com/). 
It is a subset of the of the original data, containing only interactions on recipes posted after 2008. The dataset has 731,927 
observations and 5 variables. A short description of each variable is provided below. Variables that are relevant to my analysis 
include `recipe_id`, `rating`, and `review`.

| variable | description |
|----------|-------------|
| user_id | user id |
| recipe_id | recipe id |
| date | date of interaction |
| rating | rating given |
| review | review text |

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning
To prepare the dataset for my analysis, data cleaning is necessary to ensure accurate and comprehensive results. 

#### 1. Merge datasets and replace '0' ratings
First, the two datasets—recipes and interactions—are combined using a left merge to match ratings and reviews to their corresponding recipes. This dataset is saved as `recipes_and_interactions`. Then, all `0` ratings are replaced with `np.nan` to indicate missing values, as recipes cannot have a rating of `0`. This step ensures that reviews submitted without a rating are accurately represented in the data. 

#### 2. Find the average rating of each recipe and add it to the dataset
With multiple ratings per recipe, the data needs to be aggregated to get an accurate representation of the overall rating for each recipe. This is done by grouping the merged dataset by `recipe_id` using the mean aggregation method. The series representing mean ratings is subsequently added back into the dataset under the column `avg_rating`.

#### 3. Convert string objects to lists
The columns `tags`, `steps`, `ingredients`, and `nutrition` contain values that look like lists but are actually strings. These columns are converted into lists to allow for easy access to elements if needed in future analysis. 

#### 4. Create a new column for every unique element in the `nutrition` column
The nutrition column contains information about the recipe listed in the form: [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)]. To improve readability and facilitate easier access for future analysis, each element in `nutrition` is split into individual columns.

#### 5. Create new columns for the average length of each step and the length of the descriptions
To understand whether different factors of recipe length influence ratings, it is helpful to have information about the average length of each step and the length of descriptions for each recipe. These columns are generated using the `description` and `steps` columns of the original dataset. The two new variables are stored under `des_len` and `avg_step_len`.

#### 6. Create a new column for the rounded average rating of each recipe
The average rating of each recipe is calculated by finding the mean of ratings after grouping by each recipe. However, due to disparities in the number of ratings and general rating distribution, the `avg_rating` column consists of 357 unique values. To facilitate easier comparison and interpretation of recipe ratings in future analysis, the average ratings are rounded to their nearest 0.5 increments. These rounded values are stored in the `rounded_avg_rating` column.

#### 7. Convert `submitted` column into datetime objects
Values in `submitted` are currently stored as strings in the YYYY-MM-DD format representing the date of recipe submission. To facilitate potential future analysis with the date values, this column is converted into a pandas datetime object.

#### 8. Create a column to indicate if a recipe is highly rated
The average ratings of recipes range from 1-5. For this anylsis, a highly-rated recipe is defined as a recipe with an average rating of 4 of higher. By incorporating an `is_highly_rated` column, it can facilitate future analyses where I potentially examine the features that differentiate between highly-rated and non-highly-rated recipes. 

After cleaning, the data is stored in `rated_recipes`. The cleaned data has 83,782 observations and 23 variables. The data types of each variable are shown below. 

|                     | 0              |
|:--------------------|:---------------|
| name                | object         |
| id                  | int64          |
| minutes             | int64          |
| contributor_id      | int64          |
| submitted           | datetime64[ns] |
| tags                | object         |
| n_steps             | int64          |
| steps               | object         |
| description         | object         |
| ingredients         | object         |
| n_ingredients       | int64          |
| avg_rating          | float64        |
| calories            | float64        |
| total_fat (pdv)     | float64        |
| sugar (pdv)         | float64        |
| sodium (pdv)        | float64        |
| protein (pdv)       | float64        |
| saturated_fat (pdv) | float64        |
| carbohydrates (pdv) | float64        |
| des_len             | float64        |
| avg_step_len        | float64        |
| rounded_avg_rating  | float64        |
| is_highly_rated     | bool           |
| des_cat             | object         |
| step_len_cat        | object         |


### Univariate Analysis
To gain a better understanding of the `rated_recipes` dataset before further analysis, I examined a few variables and their distributions. 

#### Distribution of Average Ratings
The plot below shows the distribution of average ratings for all the recipes in the dataset.

<iframe
  src="assets/avgratingdis.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

This plot reveals a left-skewed distribution, indicating that the majority of average recipe ratings are generally high, falling in the range of 4 to 5. Five is by far the most common rating, also suggesting that users are typically satisfied with recipes on food.com. 

#### Distribution of Description Lengths
The next plot below shows the distribution of description length for all recipes in the `rated_recipes` dataset.

<iframe
  src="assets/des_len_dis.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

The distribution is right-skewed, indicating the presence of outliers in the dataset. Since my analysis is concerned with the potential impact of recipe length on ratings, these extreme values in the `des_len` column could significantly skew my results. Therefore, to maintain accuracy in my analysis, it is necessary to address these outliers by removing extreme values. 

In this analysis, an outlier is thus defined as any value above the 99th percentile of its distribution. This means that values in the top 1% of the `des_len` column are removed. The new distribution without outliers is shown below.
In this analysis, an outlier is thus defined as any value above the 99th percentile of its distribution. This means that values in the top 1% of the `des_len` column are removed. The new distribution without outliers is shown below.

<iframe
  src="assets/des_len_dis_out.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

Without the presence of outliers, the distribution of recipe description length is still slightly right-skewed. It is observed that as description lengths increase, the number of recipes decreases. 

#### Distribution of Average Step Length
The histogram below represents the distribution of the average length of steps for each recipe in the dataset. 

<iframe
  src="assets/avg_step_dis.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

Again, the distribution is right-skewed and there are outliers in the dataset. The same procedure for handling outliers is applied to this distribution. The histogram below depicts the distribution of average step length after removing outliers. 

<iframe
  src="assets/avg_step_dis_out.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

The new plot reveals a normal distribution for average step length in `rated_recipes`.


### Bivariate Analysis
To understand the potential relationships existing between variables in the dataset, I generated some plots to explore trends between two columns.

#### Average Ratings vs. Description Length
The first scatterplot below depicts the relationship between average ratings and description length. 

<iframe
  src="assets/rating_vs_des_len.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

There does not appear to be a clear relationship between these two variables. The range of description lengths varies across 
different average ratings. 


#### Average Ratings vs. Average Step Length
The next scatterplot represents the relationship between average ratings and average length of steps. 

<iframe
  src="assets/rating_vs_step_len.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Again, there is no clear relationship and the range of description lengths varies across different average ratings.


#### Average Ratings vs. Number of Steps
The last scatterplot depicts the relationship between the number of steps and average ratings. 

<iframe
  src="assets/rating_vs_n_steps.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

There appears to be a slight upward trend in the graph, suggesting that recipes with more steps are higher rated. However, the pattern does not seem to be significant. 


### Interesting Aggregates

Another approach to gain more insights from the dataset is through aggregated data. Some interesting aggregated statistics for `rated_recipes` are generated below.

#### Means by Rounded Average Rating
The first dataset shows the average mean values of different variables after grouping by `rounded_avg_rating`. It is observed that the mean number of ingredients is consistent for all average ratings. Additionally, the average step length is noticeably higher for recipes rated 1.5 compared to other ratings. Recipes rated 2.5 also appear to have higher description lengths on average.

|   rounded_avg_rating |   minutes |   n_steps |   n_ingredients |   des_len |   avg_step_len |
|---------------------:|----------:|----------:|----------------:|----------:|---------------:|
|                  1   |   97.268  |  10.5271  |         9.05779 |   209.346 |        53.6768 |
|                  1.5 |   39.5263 |  11.3684  |         8.89474 |   149.053 |        58.9467 |
|                  2   |   94.8388 |  10.741   |         9.19707 |   207.727 |        53.6271 |
|                  2.5 |  119.856  |  10.2312  |         9.0625  |   235.032 |        52.4362 |
|                  3   |   95.8015 |   9.92117 |         9.12485 |   197.251 |        52.8256 |
|                  3.5 |   96.7808 |  10.0863  |         9.29313 |   220.611 |        53.0992 |
|                  4   |  112.89   |   9.94128 |         9.31047 |   199.301 |        53.4685 |
|                  4.5 |   87.2587 |   9.60725 |         9.1363  |   217.225 |        54.0693 |
|                  5   |  114.698  |  10.0945  |         9.15611 |   209.27  |        54.3474 |

#### Means by Year
The data frame below shows the averages of each variable by year, starting from 2008 and going until 2018. Looking at the values, most variables generally stay consistent throughout the years, including n_steps, avg_rating, and avg_step length. However, in the past two years, there appears to be a spike in the nutrition features, with calories, total_fat (pdv), sugar (pdv), sodium (pdv), protein (pdv), saturated_fat (pdv), and carbohydates (pdv) all seeing a significant increase in value. Another interesting observation to note is that description length has seen a steady decrease starting from 2014. This suggests that long descriptions may be less common or less preferred over time, possibly indicating a shift towards more concise recipes.

| submitted           |     id |   minutes |   contributor_id |   n_steps |   n_ingredients |   avg_rating |   calories |   total_fat (pdv) |   sugar (pdv) |   sodium (pdv) |   protein (pdv) |   saturated_fat (pdv) |   carbohydrates (pdv) |   des_len |   avg_step_len |   rounded_avg_rating |   is_highly_rated |
|:--------------------|-------:|----------:|-----------------:|----------:|----------------:|-------------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|----------:|---------------:|---------------------:|------------------:|
| 2008-12-31 00:00:00 | 309281 |  101.453  | 435145           |   9.64828 |         8.97479 |      4.59756 |    416.814 |           31.3343 |       68.0833 |        27.4375 |         32.3693 |               39.2794 |               13.3782 |   205.714 |        52.9977 |              4.60306 |          0.909579 |
| 2009-12-31 00:00:00 | 375235 |   90.9404 | 586720           |  10.0258  |         9.19737 |      4.62328 |    427.001 |           32.3326 |       67.4086 |        27.3593 |         33.1866 |               40.1123 |               13.6719 |   218.154 |        53.9732 |              4.62741 |          0.906791 |
| 2010-12-31 00:00:00 | 424682 |  122.742  | 666638           |   9.99182 |         9.22517 |      4.64059 |    416.476 |           31.7638 |       64.0003 |        26.9092 |         31.6399 |               38.1535 |               13.4205 |   212.655 |        54.5796 |              4.64503 |          0.904418 |
| 2011-12-31 00:00:00 | 458043 |  241.067  | 723903           |  10.15    |         9.23763 |      4.67631 |    453.516 |           34.2605 |       74.1669 |        30.7819 |         34.3239 |               41.5412 |               14.879  |   200.021 |        55.0985 |              4.67943 |          0.917419 |
| 2012-12-31 00:00:00 | 481358 |   97.9557 | 809047           |  10.7766  |         9.57259 |      4.66757 |    425.865 |           32.6721 |       66.5078 |        30.7283 |         33.174  |               40.4922 |               13.432  |   203.212 |        55.8837 |              4.66918 |          0.900695 |
| 2013-12-31 00:00:00 | 501607 |   78.4759 |      2.84056e+07 |  11.0552  |         9.65796 |      4.65713 |    454.037 |           35.8588 |       70.9861 |        32.4071 |         34.7437 |               41.914  |               14.0367 |   203.633 |        56.0694 |              4.65854 |          0.900408 |
| 2014-12-31 00:00:00 | 515407 |  100.662  |      3.07346e+08 |  11.6485  |         9.62661 |      4.60617 |    476.394 |           38.3416 |       71.0477 |        32.6495 |         36.429  |               44.7269 |               14.4945 |   194.49  |        56.13   |              4.61035 |          0.854022 |
| 2015-12-31 00:00:00 | 522807 |   96.8704 |      9.09957e+08 |  12.6478  |        10.495   |      4.68933 |    455.074 |           36.897  |       60.2857 |        37.2126 |         35.1628 |               46.6545 |               13.5449 |   186.797 |        57.0935 |              4.68985 |          0.813953 |
| 2016-12-31 00:00:00 | 527627 |  203.718  |      7.37309e+08 |  12.0667  |         9.55897 |      4.51141 |    546.194 |           39.2    |       90.1026 |        40.5077 |         37.7385 |               48.5282 |               19.2923 |   160.313 |        57.7649 |              4.51524 |          0.74359  |
| 2017-12-31 00:00:00 | 531913 |  102.636  |      6.933e+08   |  13.5371  |        10.0177  |      4.46943 |    739.428 |           56.3039 |      157.134  |        38.1767 |         52.1767 |               73.3781 |               25.1449 |   132.11  |        60.4824 |              4.46934 |          0.65371  |
| 2018-12-31 00:00:00 | 536022 |  128.663  |      7.80266e+08 |  18.8098  |        12.2011  |      4.61825 |    996.127 |           73.8043 |      262.707  |       216.326  |         51.5815 |              110.663  |               38.5272 |   136.625 |        57.8146 |              4.62324 |          0.690217 |

## Assessment of Missingness
Following some basic exploration of the dataset, it is also important to analyze the missing data to gain deeper insights. In the merged dataset, three columns contain missing values: `description`, `rating`, and `review`.

### NMAR Analysis
NMAR, or Not Missing At Random, refers to missingness in datasets that is related to the value of the data point itself. In the merged dataset, I believe values in the `review` column are NMAR. Leaving a review is not an act that can be done with a simple click of a button. Users need time to process the recipe and formulate their opinion writing out a review. Therefore, if a user does not feel too strongly about a recipe or does not care to contribute, they are less likely to leave a review. This results in missing reviews that are related to a user's personal feelings and engagement with the recipe. If there were additional information in the dataset, such as a column that captures a user's typical engagement levels on the site, it could potentially make this missingness MAR, or Missing At Random. In this context, users who typically exhibit low engagement levels on the site may be less inclined to leave reviews on the recipes. 

### Missingness Dependency
Online recipes are often characterized by lengthy introductions and profound backstories that accompany each recipe. However, some recipes in our dataset are missing descriptions. Further exploration must be done to uncover whether this missingness is related to any other variable in our dataset, making it potentially MAR.

First, missingness in `description` is examined in relation to `avg_rating`.

**Null hypothesis:** The missingness in `description` is unrelated to `avg_rating`. 

**Alternate hypothesis:** The missingness in `description` is related to `avg_rating` (missingness is MAR). 

**Test Statistic:** The absolute difference in means of `avg_rating` between rows with missing descriptions and rows without missing description

The significance level is defined as **0.05**.

<iframe
  src="assets/missingness_test_stats.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

After running a permutation test with 1000 simulated test statistics, the resulting p-value is 0.129. Since this falls below the significance level of 0.05, we fail to reject the null hypothesis and cannot attribute the missingness in `description` to being MAR, dependent on `avg_rating`.


Next, missingness in `description` is examined in relation to `avg_step_len`.

**Null hypothesis:** The missingness in `description` is unrelated to `avg_step_len`. 

**Alternate hypothesis:** The missingness in `description` is related to `avg_step_len` (missingness is MAR). 

**Test Statistic:** The absolute difference in means of `avg_step_len` between rows with missing descriptions and rows without missing description

The significance level is defined as **0.05**.

<iframe
  src="assets/missingness_test_stats2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Following a permutation test of 1000 simulated test statistics, the resulting p-value is 0.002. This value is less than the significance level of 0.05. Therefore, we reject the null hypothesis, suggesting that missingness in `descriptions` is indeed MAR and dependent on `avg_step_len`. Looking at the above plot, it can be seen that the observed statistic is much greater than the majority of test statistics simulated during the permutation tests. This further illustrates that the p-value is significant.

## Hypothesis Testing
After gaining a comprehensive understanding of the dataset, I began my analysis to answer the question of interest: __what factors correlelate with a highly rated recipe? Are recipes with longer descriptions rated significantly higher than recipes with shorter descriptions?__

To answer this question, a permutation test can be performed with the following information: 

**Null hypothesis:** There is no significant difference in the average ratings between recipes with a shorter description (<= 75th percentile) and recipes with a long description (> 75th percentile).  

**Alternate hypothesis**: Recipes with long descriptions (> 75th percentile) have significantly higher average ratings compared to recipes with shorter (<= 75th percentile) descriptions.

**Test statistic:** The difference in mean average ratings between recipes that have long descriptions and recipes that have short descriptions. 

The significance level is **0.05%**

<iframe
  src="assets/hypothesis_test_stats.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

After running the permutation test, the resulting p-value is 0.064. While this value falls super close to the defined significance level of 0.05, it is still above the threshold. Therefore, **we fail to reject the null hypothesis**, suggesting that there isn't a significant difference in the average ratings between recipes with shorter descriptions (≤ 75th percentile) and those with longer descriptions (> 75th percentile).

## Framing a Prediction Problem
After some basic data exploration and hypothesis testing, I can move on to frame a relevant prediction problem based on our dataset. Staying with the theme of recipe ratings, an interesting question to examine is whether a model can **predict if a recipe is highly rated**. Here, a highly rated recipe is defined as a recipe with an average rating of 4 or higher. Since the prediction task involves distinguishing between two categories: highly-rated and non-highly-rated, this is a **binary classification** problem. I can address this question by building a Decision Tree Classifier trained on our dataset features.

The response variable in this problem is a binary indicator for whether a recipe is highly rated or not. This is represented by the `is_highly_rated` column in the `rated_recipes` dataset. Values in this column are determined by comparing `avg_rating` to a threshold of 4. `True` indicate that a recipe is highly-rated and `False` indicate the opposite. I chose this as the response variable because it provides a straightforward distinction between highly rated recipes and others, making the classification problem clear. 

In building this classification model, all the features in the `rated_recipes` dataset are available at the time of prediction. This is because the dataset consists of recipes from food.com, all of which have been publicly shared with their corresponding features. At the time of prediction, there are no unavailable features except for those values that were originally missing from the posted recipe. 

To evaluate my model, I will use the F1-score. This metric is appropriate for my classification model because it provides a comprehensive evaluation of the model's performance by considering both precision and recall. In the previous exploration of the dataset above, it was observed that the average rating distribution is left-skewed, indicating that the majority of recipe ratings are generally high, falling in the range of 4 to 5. Therefore, to account for the imbalance in the dataset, F1-score is a suitable choice as it balances the trade-off between false positives and false negatives.

## Step 6: Baseline Model
Before building the Decision Tree Classifier model, the `rated_recipes` dataset is split into training and testing sets using a 75/25 split. Additionally, to maintain consistency and keep the same train and test sets for the final model, `X` and `y` are stored with all the features of the dataset.

In building my baseline model, I used a Decision Tree classifier trained on three features of the dataset. Of the features, two are quantitative: `avg_step_len` and `minutes` and one is nominal categorical: `des_cat`. From earlier, `des_cat` is a column that labels the recipe either 'long' or 'short' depending on if the description length falls above or below the 75th percentile of recipes. In training the model, `des_cat` will be one hot encoded to transform the categorical values into numerical values suitable for the model. The first column is also dropped to avoid multicollinearity. All other numerical features will be left as-is. 

After evaluating the baseline model using F1-score as a metric, the training set score is approximately 0.97 while the testing set score is approximately 0.83. Although both scores are quite high, further fine-tuning of the hyperparameters could help achieve an even better test set score. Looking at the high F1 score for the training set, it suggests that the decision tree classifier model may be overfitting to the training set. Addressing this issue could lead to a model with better generalizability and a higher F1 score on the test set.

## Step 7: Final Model
Before enhancing the baseline model and fine-tuning its parameters, it is helpful to understand some general features that distinguish highly-rated recipes from non-highly-rated ones. The data frame below displays the means of all features in rated_recipes, grouped by the `is_highly_rated column`. Scanning the table allows for a basic comparison of the key differing features between highly-rated and non-highly-rated recipes. Namely, it is observed that highly-rated recipes generally have shorter cooking times (minutes), lower calories, lower sugar values, and lower sodium values. This information could potentially help optimize the final classification model.

| is_highly_rated   |     id |   minutes |   contributor_id |   n_steps |   n_ingredients |   avg_rating |   calories |   total_fat (pdv) |   sugar (pdv) |   sodium (pdv) |   protein (pdv) |   saturated_fat (pdv) |   carbohydrates (pdv) |   des_len |   avg_step_len |   rounded_avg_rating |
|:------------------|-------:|----------:|-----------------:|----------:|----------------:|-------------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|----------:|---------------:|---------------------:|
| False             | 386625 |   137.275 |      3.58231e+07 |  10.5607  |         9.26894 |      2.79124 |    465.214 |           34.8172 |       83.9355 |        32.7109 |         33.2135 |               43.8151 |               15.7436 |   208.819 |        53.4742 |              2.79165 |
| True              | 380617 |   110.707 |      1.27413e+07 |   9.99943 |         9.17897 |      4.75434 |    424.833 |           32.282  |       67.1195 |        28.2437 |         32.9825 |               39.7527 |               13.5492 |   208.51  |        54.1569 |              4.75891 |

 After exploring potential features for my final model, I settled on five: `minutes`, `calories`, `sugar (pdv)`, `contributor_id`, and `step_len_cat`. Looking at the aggregated data frame above, I saw that `minutes`, `calories`, and `sugar (pdv)` differed most significantly in recipes that were highly rated compared to those that weren't. Therefore, I assumed these features are valuable predictors in classifying between the rated recipes. 
 
Specifically for `sugar (pdv)` and `calories`, I noticed that lower values tended to correlate with higher ratings. This could be because recipes with lower sugar and calorie content are perceived to be healthier, which may contribute to higher ratings. These values are passed as-is into my model.

Shorter cooking times, or `minutes` were also observed to be correlated with highly-rated recipes. This could be attributed to users who favor simpler and more concise recipes. Being able to create a dish faster may also result in instant gratification, further explaining the higher ratings. To use this feature in my model, I used a standard scaler to transform the values. This accounts for all the outliers in `minutes` that may influence classification results.

As for `contributor_id`, I noticed that highly-rated recipes also had significantly "lower" id numbers. While the dataset does not explicitly state how user IDs are assigned, it appeared that IDs in the lower range tended to be associated with recipes that received higher ratings. If IDs are indeed assigned in ascending order, this observation could suggest that early contributors tend to create recipes that are well-received. For this feature, I transformed it using a Binarizer with a threshold of `16000000`. This decision is aimed at splitting early contributors from more recent ones.

My last feature is `des_cat`. This column labels a recipe `short` or `long` depending if its description length falls above or below the 75th percentile. From previous explorations, I found that recipes with longer descriptions also had higher ratings. While the effect was not significant at the 0.05 threshold, it often fell really close, which is why I believe this feature would still be helpful. This feature is one hot encoded to transform the categorical values into numerical ones for my model.


For my baseline model, I noticed that the training F1 score was particularly high, indicating a likely overfitting to the training set. Therefore, instead of a decision tree classifier, I opted for a random forest classifier. This model is more robust to overfitting. After finalizing my features, I performed a Grid Search CV to identify the optimal hyperparameters for my model. I tested various increments of estimators (ranging from 30 to 100) and different max depths (10, 15, 20, 25, 30, None) to fine-tune the hyperparameters. When searching for optimal hyperparameters, I also made sure to include max depth as an option to help control the complexity of the model and avoid overfitting. 

My final ideal hyperparameter is a random forest classifier with `30` estimators and a max depth of `10`. Using this new model, my F1 score went from approximately 0.83 in the baseline model to approximately 0.86. While this not does appear to be a significant increase, I observed noticeable improvements when examining precision and recall scores separately. From an initial recall score of 0.83 in the baseline model, the recall score jumped to 0.90 in the final. This indicates that the refined model is better at correctly identifying highly-rated recipes with reduced false negative predictions. Precision scores also saw slight improvements (from 0.82 to 0.84), demonstrating that the final model has less false positive classifications. Finally, compared to the baseline model, this model does not overfit the training dataset with a training set F1 score of approximately 0.86.

## Step 8: Fairness Analysis
Besides testing for accuracy, an important aspect of model evaluation is fairness analysis. To test the fairness of my model, I plan to examine if my model fairly makes predictions for healthy and unhealthy recipes. For the purpose of this test, healthy recipes are defined as recipes with `saturated_fat (pdv)` content less than or equal to the 50th percentile and unhealthy recipes are those with `saturated_fat (pdv)` greater than the 50th percentile. Again, to account for the imbalance in rating distributions, the F1 score will be used as the metric to evaluate performance across these two groups. A permutation test with the following information is performed:

**Null hypothesis:** The model fairly predicts if a recipe is highly rated for "healthy" and "unhealthy" recipes. There is no significant difference in the F1 scores of the model between the two groups.

**Alternate hypothesis:** The model unfairly predicts if a recipe is highly rated for "healthy" and "unhealthy" recipes. There is a significant difference in the F1 scores of the model between the two groups.

**Test statistic:** the absolute difference in F1 scores

The significance level is **0.05**.

<iframe
  src="assets/fairness_test_stats.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

With a p-value of 0.292, we fail to reject the null hypothesis. This outcome suggests that there is no significant difference in the model's performance between "healthy" and "unhealthy" recipes, indicating that the model achieves parity and does not exhibit bias towards either category. This is the ideal result, suggesting that the model is effective across different recipe types.


















