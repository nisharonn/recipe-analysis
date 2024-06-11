# The Recipe to a Good Recipe

Author: Sharon Ni (s3ni@ucsd.edu)

This project analyzes the features that correlate with online recipe ratings. It is part of the coursework for DSC80: 
Practice and Application of Data Science at UCSD. 

## Introduction:
Cooking is a popular hobby for many, offering not only the joy of creating delicious meals but also the satisfaction of sharing
these creations with friends and family. With the expanding reach of online platforms, sharing recipes has become much easier
and more widespread. Specifically, through the popular website food.com, users can readily access recipes, rate them, and
interact with fellow enthusiasts who share a similar hobby. 

Following this growth, an intriguing and pertinent question emerges: __what factors correlate with a highly-rated recipe?__ As
users engage with recipes online, they are exposed to a variety of valuable information and the occasional long backstories that
seem to delve into the contributor's entire nostalgic childhood memory associated with the food. While some may appreciate the
heartfelt, lengthy introductions, others may simply be seeking concise directions. My analysis aims to examine the relationship
between recipe length—consisting of factors such as number of steps, length of directions, and length of descriptions—and recipe
ratings to uncover insights into user preferences when interacting with recipes online. This understanding can help recipe
creators enhance their content to better align with user preferences and ultimately improve the overall cooking and sharing
experience. 

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
First, the two datasets—recipes and interactions—are combined using a left merge to match ratings and reviews to their 
corresponding recipes. This dataset is saved as `recipes_and_interactions`. Then, all `0` ratings are replaced with `np.nan` to 
indicate missing values, as recipes cannot have a rating of `0`. This step ensures that reviews submitted without a rating are 
accurately represented in the data. 

#### 2. Find the average rating of each recipe and add it to the dataset
With multiple ratings per recipe, the data needs to be aggregated to get an accurate representation of the overall rating for 
each recipe. This is done by grouping the merged dataset by `recipe_id` using the mean aggregation method. The series 
representing mean ratings is subsequently added back into the dataset under the column `avg_rating`.

#### 3. Convert string objects to lists
The columns `tags`, `steps`, `ingredients`, and `nutrition` contain values that look like lists but are actually strings. These 
columns are converted into lists to allow for easy access to elements if needed in future analysis. 

#### 4. Create a new column for every unique element in the `nutrition` column
The nutrition column contains information about the recipe listed in the form: [calories (#), total fat (PDV), sugar (PDV), 
sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)]. To improve readability and facilitate easier access 
for future analysis, each element in `nutrition` is split into individual columns.

#### 5. Create new columns for the average length of each step and the length of the descriptions
To understand whether different factors of recipe length influence ratings, it is helpful to have information about the average 
length of each step and the length of descriptions for each recipe. These columns are generated using the `description` and 
`steps` columns of the original dataset. The two new variables are stored under `des_len` and `avg_step_len`.

#### 6. Create a new column for the rounded average rating of each recipe
The average rating of each recipe is calculated by finding the mean of ratings after grouping by each recipe. However, due to 
disparities in the number of ratings and general rating distribution, the `avg_rating` column consists of 357 unique values. To 
facilitate easier comparison and interpretation of recipe ratings in future analysis, the average ratings are rounded to their 
nearest 0.5 increments. These rounded values are stored in the `rounded_avg_rating` column.

### Univariate Analysis
To gain a better understanding of the `rated_recipes` dataset before further analysis, I examined a few variables and their 
distributions. 

#### Distribution of Average Ratings
The plot below shows the distribution of average ratings for all the recipes in the dataset.

This plot reveals a left-skewed distribution, indicating that the majority of average recipe ratings are generally high, falling in the range of 4 to 5. Five is by far the most common rating, also suggesting that users are typically satisfied with recipes on food.com. 

#### Distribution of Description Lengths
The next plot below shows the distribution of description length for all recipes in the `rated_recipes` dataset.

The distribution is right-skewed, indicating the presence of outliers in the dataset. Since my analysis is concerned with the potential impact of recipe length on ratings, these extreme values in the `des_len` column could significantly skew my results. Therefore, to maintain accuracy in my analysis, it is necessary to address these outliers by removing extreme values. 

In this analysis, an outlier is thus defined as any value above the 99th percentile of its distribution. This means that values in the top 1% of the `des_len` column are removed. The new distribution without outliers is shown below.

#### Distribution of Description Lengths (with outliers removed)

Without the presence of outliers, the distribution of recipe description length is still slightly right-skewed. It is observed that as description lengths increase, the number of recipes decreases. 


#### Distribution of Average Step Length
The histogram below represents the distribution of the average length of steps for each recipe in the dataset. 


Again, the distribution is right-skewed and there are outliers in the dataset. The same procedure for handling outliers is applied to this distribution. The histogram below depicts the distribution of average step length after removing outliers. 

#### Distribution of Average Step Length (with outliers removed)

The new plot reveals a normal distribution for average step length in `rated_recipes`.


### Bivariate Analysis
To understand the potential relationships existing between variables in the dataset, I generated some plots to explore trends between two columns.

#### Average Ratings vs. Description Length
The first scatterplot below depicts the relationship between average ratings and description length. 

There does not appear to be a clear relationship between these two variables. The range of description lengths varies across 
different average ratings. 


#### Average Ratings vs. Average Step Length
The next scatterplot represents the relationship between average ratings and average length of steps. 

Again, there is no clear relationship and the range of description lengths varies across different average ratings.


#### Average Ratings vs. Number of Steps
The last scatterplot depicts the relationship between number of steps and average ratings. 
There appears to be a slight upward trend in the graph, suggesting that recipes with more steps are higher rated. However, the 
pattern does not seem to be significant. 


### Interesting Aggregates

Another approach to gain more insights from the dataset is through aggregated data. Some interesting aggregated statistics for `rated_recipes` are generated below.

The first dataset shows the average mean values of different variables after grouping by `rounded_avg_rating`. It is observed 
that the mean number of ingredients is consistent for all average ratings. Additionally, the average step length is noticebly 
higher for recipes rated 1.5 compared to other ratings. Recipes rated 2.5 also appear to have higher description lengths on 
average.

|   minutes |   n_steps |   n_ingredients |   des_len |   avg_step_len |
|----------:|----------:|----------------:|----------:|---------------:|
|   95.9151 |  10.489   |         9.04924 |   226.469 |        54.3509 |
|   39.5263 |  11.3684  |         8.89474 |   149.053 |        58.9467 |
|   96.4288 |  10.7328  |         9.2272  |   215.838 |        54.1865 |
|  120.835  |  10.1768  |         9.03049 |   249.049 |        53.7148 |
|   96.3307 |   9.96728 |         9.13284 |   211.315 |        53.696  |
|   98.8281 |  10.095   |         9.30539 |   236.6   |        54.0129 |
|  112.295  |   9.96975 |         9.33595 |   213.963 |        54.3224 |
|   87.7273 |   9.68446 |         9.16116 |   233.588 |        54.9651 |
|  117.244  |  10.1464  |         9.18584 |   225.7   |        55.3098 |

The next pivot table below highlights the average rating for recipes based on the number of steps. It can be observed that the 
average rating for varying step numbers is relatively high, with all values in the data frame exceeding 4. The lowest average 
rating in this data is 4.0, which occurs when the number of steps is 61.

|   avg_rating |
|-------------:|
|      4.64813 |
|      4.66612 |
|      4.65546 |
|      4.64004 |
|      4.61038 |
|      4.61137 |
|      4.62251 |
|      4.62609 |
|      4.61521 |
|      4.60389 |
|      4.61956 |
|      4.61869 |
|      4.6287  |
|      4.60289 |
|      4.61348 |
|      4.63361 |
|      4.64033 |
|      4.6261  |
|      4.65347 |
|      4.64079 |
|      4.63482 |
|      4.64615 |
|      4.62641 |
|      4.66983 |
|      4.60421 |
|      4.61642 |
|      4.57603 |
|      4.76479 |
|      4.66187 |
|      4.53663 |
|      4.69456 |
|      4.6779  |
|      4.63761 |
|      4.65258 |
|      4.68614 |
|      4.74912 |
|      4.71939 |
|      4.65304 |
|      4.72292 |
|      4.7375  |
|      4.8001  |
|      4.82574 |
|      5       |
|      4.5375  |
|      4.73387 |
|      4.22727 |
|      4.78819 |
|      4.71429 |
|      4.60833 |
|      4.42857 |
|      4.67857 |
|      4.22222 |
|      4.57143 |
|      5       |
|      4.7375  |
|      5       |
|      4.5     |
|      4.92857 |
|      5       |
|      5       |
|      4.1875  |
|      4.66667 |
|      5       |
|      5       |
|      5       |
|      5       |
|      5       |
|      4.5     |
|      5       |
|      5       |
|      5       |
|      5       |
|      5       |
|      4.75    |
|      4.875   |
|      4.5     |
|      5       |
|      5       |
|      5       |
|      3.66667 |
|      5       |
|      5       |
|      5       |


## Assessment of Missingness
Following some basic exploration of the dataset, it is also important to analyze the missing data to gain deeper insights. In 
the merged dataset, three columns contain missing values: `description`, `rating`, and `review`.

### NMAR Analysis
NMAR, or Not Missing At Random, refers to missingness in datasets that is related to the value of the data point itself. In the 
merged dataset, I believe values in the `review` column are NMAR. Leaving a review is not an act that can be done with a 
simple click of a button. Users need time to process the recipe and formulate their opinion writing out a review. Therefore, if 
a user does not feel too strongly about a recipe or does not care to contribute, they are less likely to leave a review. This 
results in missing reviews that are related to a user's personal feelings and engagement with the recipe. If there were 
additional information in the dataset, such as a column that captures a user's typical engagement levels on the site, it could 
potentially make this missingness MAR, or Missing At Random. In this context, users who typically exhibit low engagement levels 
on the site may be less inclined to leave reviews on the recipes. 

## Hypothesis Testing
After gaining a comprehensive understanding of the dataset, we can begin our analysis to answer the question of interest: 
__what factors correlelate with a highly rated recipe? Are recipes with longer descriptions rated significantly higher than 
recipes with shorter descriptions?__

To answer this question, a permutation test can be performed with the following information: 

**Null hypothesis:** There is no significant difference in the average ratings between recipes with a shorter description (<= 
75th percentile) and recipes with a long description (> 75th percentile).  

**Alternate hypothesis**: Recipes with long descriptions (> 75th percentile) have significantly higher average ratings compared 
to recipes with shorter (<= 75th percentile) descriptions.

**Test statistic:** The difference in mean average ratings between recipes that have long descriptions and recipes that have 
short descriptions. 

The significance level is **0.05%**

After running the permutation test, the resulting p-value is 0.064. While this value falls super close to the defined 
significance level of 0.05, it is still above the threshold. Therefore, **we fail to reject the null hypothesis**, suggesting 
that there isn't a significant difference in the average ratings between recipes with shorter descriptions (≤ 75th percentile) 
and those with longer descriptions (> 75th percentile).

## Framing a Prediction Problem
After some basic data exploration and hypothesis testing, we can move on to frame a relevant prediction problem based on our 
dataset. Staying with the theme of recipe ratings, an interesting question to examine is whether we can **predict the average 
rating of recipes** given the available features of our dataset. While the response variable—average rating—is continuous, 
ratings are typically best interpreted in ordinal categories. Therefore, instead of predicting the precise average rating of 
each recipe, we can opt to predict the rounded average rating from the following discrete set of ratings: [1, 1.5, 2, 2.5, 3, 
3.5, 4, 4.5, 5]. This question is now a **multi-class classification** problem and we can address it by building a Random 
Forest Classifier trained on our dataset features.

As mentioned, the response variable in this problem is the rounded average rating, specifically represented by the `rounded_avg_rating` column in the recipes dataset. This value reflects the collective responses to each recipe, serving as a strong general indicator of a recipe's overall quality. Hence, as a key metric, this variable can be expected to correlate with other features in the dataset, which is why I chose it as a response variable.  

In building this classification model, all the features in the `rated_recipes` dataset are available at the time of prediction. This is because the dataset consists of recipes from food.com, all of which have been publicly shared with their corresponding features. At the time of prediction, there are no unavailable features except for those values that were originally missing from the posted recipe. 

To evaluate our model, we will use the F1-score. This metric is appropriate for our classification model because it provides a comprehensive evaluation of the model's performance by considering both precision and recall. In the previous exploration of the dataset above, it was observed that the average rating distribution is left-skewed, indicating that the majority of recipe ratings are generally high, falling in the range of 4 to 5. Therefore, to account for the imbalance in our dataset, the F1-score is a suitable choice as it balances the trade-off between false positives and false negatives.









