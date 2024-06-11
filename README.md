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

The dataset below, titled `recipe`, contains recipes from the [food.com](https://www.food.com/). It is a subset of the 
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



