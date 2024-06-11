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
The plot below shows the distribution of average ratings for all the recipes in the dataset.





