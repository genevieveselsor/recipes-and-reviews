# An Exploration of the Relationship Between a Recipe's Convenience and Rating
Authors: Genevieve Selsor and Mena Song-Lew

## Introduction

As college students with endless assignments, tests, and projects, thinking about what our next meal is going to be is usually one of our last priorities in our day to day lives. Food from the dining halls can get pretty old pretty fast, so students often rely on meals that are convenient to make. We’ll measure convenience by the time to prepare a recipe, number of steps, and number of ingredients. A quick, easy, and yummy meal is something we all seek. In this setting, a student is looking for recipes with low preparation time, few steps and ingredients, and a high rating. With this in mind, **we would like to investigate the rating of the meal recipe depending on its preparation time. Specifically, if meals with low preparation time are rated higher compared to those with higher preparation time**.


To perform our analysis, we utilize two datasets containing recipes and ratings from [food.com](https://www.food.com/), which was originally scraped for the use of the research paper, [Generating Personalized Recipes from Historical User Preferences](https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf) by Majumder et al.

The `recipes` dataset contains 83782 rows of unique recipes with 12 columns consisting of the following information:

| Column |	Description |
| ------ | -------|
| `'name'` |	Recipe name |
| `'id'` |  Recipe ID |
| `'minutes'` |	Minutes to prepare recipe |
| `'contributor_id'` |	User ID who submitted this recipe |
| `'submitted'` |	Date recipe was submitted |
| `'tags'` |	Food.com tags for recipe |
| `'nutrition'` |	Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'` |	Number of steps in recipe |
| `'steps'` |	Text for recipe steps, in order |
| `'description'` |	User-provided description |
| `'ingredients'` | Ingredients used in recipe |	
| `'n_ingredients'` | Number of ingredients in recipe |

The `interactions` dataset contains 731927 rows of reviews submitted from a user regarding a specific recipe. There are 5 columns containing the following information:

| Column |	Description |
| ------ | ------|
| `'user_id'` |	User ID |
| `'recipe_id'` |	Recipe ID |
| `'date'` |	Date of interaction |
| `'rating'` |	Rating given |
| `'review'` |	Review text |

## Data Cleaning and Exploratory Data Analysis

We believe that some of the most relevant columns to answer our question are `'minutes'`, `'n_steps'`, `'n_ingredients'`, and `'rating'`, as described above, along with the addition of the `'average_rating'` column, which is the average of the ratings for each unique recipe.

### Data Cleaning

Before beginning our analysis, we perform the following data cleaning steps to our datasets:

* Left merge the `recipes` and `interactions` datasets together
   * This step allows us to match each recipe with their respective ratings and reviews, while keeping the recipes that might not have been rated in our dataset

* Convert ratings of 0 to `np.nan`
   * Since ratings are conducted on a scale from 1 (lowest) to 5 (highest), a rating of 0 would indicate that the respective rating is missing. Therefore, we replace ratings of 0 with `np.nan` to avoid bias in any further calculations

* Convert `'tags'` and `'nutrition'` columns to lists
   * Since these columns are formatted as strings that look like list, we convert them to actual lists in order to extract usable data and meaningful information out of them

* Add the following columns `'avg_rating'`, `'is_easy'`, `'calories'`, `'protein'`, and `'saturated_fat'`
  * `'avg_rating'`: Since a recipe can have ratings from multiple different users, we take the average of all of its ratings as a metric of a recipe's overall rating
  * `'is_easy'`: Column of booleans containing indicating whether or not a recipe has the word 'easy' in its tags, extracted from the `'tags'` column
  * `'calories'`: Total number of calories in a recipe, extracted from the `'nutrition'` column
  * `'protein'`: PDV (percentage daily value) of protein in a recipe, extracted from the `'nutrition'` column
  * `'saturated_fat'`: PDV (percentage daily value) of saturated fats in a recipe, extracted from the `'nutrition'` column

* Drop irrelevant columns
   * The columns we keep for our analysis are: `'name'`, `'recipe_id'`, `'user_id'`, `'minutes'`, `'n_steps'`, `'n_ingredients'`, `'rating'`, `'avg_rating'`, `'is_easy'`, `'calories'`, `'protein'`, and `'saturated_fat'`


| name                                 |   recipe_id |          user_id |   minutes |   n_steps |   n_ingredients |   rating |   avg_rating | is_easy   |   calories |   protein |   saturated_fat |
|:-------------------------------------|------------:|-----------------:|----------:|----------:|----------------:|---------:|-------------:|:----------|-----------:|----------:|----------------:|
| 1 brownies in the world    best ever |      333281 | 386585           |        40 |        10 |               9 |        4 |            4 | False     |      138.4 |         3 |              19 |
| 1 in canada chocolate chip cookies   |      453467 | 424680           |        45 |        12 |              11 |        5 |            5 | False     |      595.1 |        13 |              51 |
| 412 broccoli casserole               |      306168 |  29782           |        40 |         6 |               9 |        5 |            5 | True      |      194.8 |        22 |              36 |
| 412 broccoli casserole               |      306168 |      1.19628e+06 |        40 |         6 |               9 |        5 |            5 | True      |      194.8 |        22 |              36 |
| 412 broccoli casserole               |      306168 | 768828           |        40 |         6 |               9 |        5 |            5 | True      |      194.8 |        22 |              36 |


### Univariate Analysis

We decide to examine the distribution of minutes needed to complete a recipe for each unique recipe. We filter our data so that each recipe is only included once. As the plot below shows, the distribution is heavily skewed to the right, and a trend is difficult to see with the presence of outliers.

<iframe
  src="assets/fig.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We create a subset dataframe called `'without_outliers'` by using the Inter Quartile Range (IQR) method to visualize the cooking time in minutes without outliers. As we can see the data is skewed right, with many recipes taking about an hour or less to complete.

<iframe
  src="assets/fig_no_outliers.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


### Bivariate Analysis

We chose to examine the relationship between a recipe's average rating and the number of minutes it takes to make. We again use the `'without_outliers'` dataframe to properly examine the relationship without bias outliers from `'minutes'`. As we can see in the plot below, many of the recipes that take less time to make are rated highly, however since most of our data is gathered here, we can't say that there is a strong correlation between minutes and average rating. There may be a correlation between low time getting MORE ratings than high time. This is different from our assumption that low time gets high ratings and high time gets low ratings. Instead the plot shows that low time gets MORE ratings, and high time gets LESS ratings, where in both cases ratings are high. This may come from more users trying the low time recipe, and users only rating if they believe a recipe is really good or really bad. 

<iframe
  src="assets/fig4.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Note that there is a grid-like pattern and lots of data at discretevalues because time and rating are user inputs. An explanation for this trend in time is that a recipe creator will be more likely to input a rounded number to generally convey an idea of time instead of stating exact time due to variance of equipment and or skill of the recipe follower. This trend is also seen in the average rating statistic because some recipes may have only one or two ratings, which results in averages on multiples of 0.5, or the ratings for a recipe are unanimous.

### Interesting Aggregates

We performed a `.mean()` aggregate to find the average amount of `'minutes'` (without outliers), `'n_steps'`, and `'n_ingredients'` for each rating. As we can see in the table below, the average number of minutes is lower for better rated recipes than it is for worse rated recipes. The same relationship follows for the number of steps. However, the average number of ingredients tends to fluctuate based on the rating, being higher for average-rated recipes, and lower for 1-star and 5-star recipes.

|   avg_minutes |   avg_n_steps |   avg_n_ingredients |
|--------------:|--------------:|--------------------:|
|       99.6725 |      10.63    |             8.91289 |
|       98.0215 |      10.6976  |             9.22973 |
|       87.4976 |       9.99205 |             9.20092 |
|       91.585  |       9.57743 |             9.10076 |
|      106.924  |       9.9849  |             9.04675 |
|      106.79   |      10.0178  |             9.07151 |

Note that there does not exist Simpson's Paradox between any of the columns presented. Hence, we are able to aggregate by rating without consequence.

## Assessment of Missingness

There are three columns within our merged dataset that contain a large amount of missing values: `'description'`, `'rating'`, and `'review'`.

### NMAR Analysis

We believe that the `'review'` column is Not Missing at Random (NMAR) because people would be less likely to write a review for a recipe that they felt indifferent about. Usually people only leave reviews if they feel an extreme opinion towards a recipe, such as if they loved or hated it, as writing out an entire review is more time consuming than simply just leaving a rating. So, the missingness of the `'review'` column is dependent on itself.

Additional data we could obtain is the time spent filling out a rating and review. A lower time would be explained by a user only completing a rating, and a higher time would be explained by the time spent to write a  review. Having this feature in the dataset would change `'review'`’s missingness to Missing at Random (MAR).

### Missingness Dependency

We move on to test if the missingness of `'rating'` depends on the column `'minutes'`.

**Null Hypothesis:** The distribution of `'minutes'` when `'rating'` is missing is the same as the distribution of `'minutes'` when `'rating'` is not missing.

**Alternative Hypothesis:** The distribution of `'minutes'` when `'rating'` is missing is NOT the same as the distribution of `'minutes'` when `'rating'` is not missing.

**Test Statistic:** The absolute difference of mean in minutes of the group when `'rating'` is missing and that of the group when `'rating'` is not missing.

**Significance Value:** 0.05

Below are the observed distributions of `'minutes'` when `'rating'` is missing (True) and not missing (False) (rescaled for visualization):

<iframe
  src="assets/fig6.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We run a permutation test by shuffling the missingness of rating and collecting the absolute difference in the means of minutes between the two distributions described above 1000 times. Our observed test statistic is **51.4524**, which is indicated by the red line on the graph below. We obtain a p-value of **0.117**, which is greater than our significance level of 0.05. Therefore, we **fail to reject the null hypothesis**. The missingness of `'rating'` does NOT depend on `'minutes'`.

<iframe
  src="assets/fig7.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We now move on to test if the missingness of `'rating'` depends on the column `'n_steps'`.

**Null Hypothesis:** The distribution of `'n_steps'` when `'rating'` is missing is the same as the distribution of `'n_steps'` when `'rating'` is not missing.

**Alternative Hypothesis:** The distribution of `'n_steps'` when `'rating'` is missing is NOT the same as the distribution of `'n_steps'` when `'rating'` is not missing.

**Test Statistic:** The absolute difference of mean in the number of steps of the distribution of the group when `'rating'` is missing and the distribution of the group when `'rating'` is not missing.

**Significance Value:** 0.05

Below are the observed distributions of `'n_steps'` when `'rating'` is missing (True) and not missing (False):

<iframe
  src="assets/fig8.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We run a permutation test by shuffling the missingness of rating and collecting the absolute difference in the means of the number of steps between the two distributions described above 1000 times. Our observed test statistic is **1.3386**, which is indicated by the red line on the graph below. We obtain a p-value of **0.0**, which is less than our significance level of 0.05. Therefore, we **reject the null hypothesis**. The missingness of `'rating'` does depend on `'n_steps'`.

<iframe
  src="assets/fig9.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing

As mentioned in our introduction, we are investigating the relationship between a meal's rating and its preparation time. We specifically want to assess if meals that take an hour (60 minutes) or less to make are rated higher than meals that take longer to make. To investigate this, we run a **permutation test** with the following objectives:

**Null Hypothesis:** Recipes are rated the same regardless of the amount of time needed to cook them.

**Alternative Hypothesis:** Recipes that take an hour or less to make have a higher rating than those that take longer to cook.

**Test Statistic:** Difference in mean rating between recipes that take an hour or less to make and recipes that take more than an hour to make.

**Significance Level:** 0.05

To perform this permutation test, we split the recipes into two groups: those that take 60 minutes or less to make and those that take more than 60 minutes to make. Our **observed statistic** is **0.0285**. We then run 1000 simulations to generate an empirical distribution of the test statistic under the null hypothesis.

<iframe
  src="assets/fig10.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We obtain a p-value of **0.0** which is less than our significance level of 0.05. Therefore we **reject the null hypothesis**. This suggests that people do not rate recipes regardless of the amount of time needed to cook them; it seems that recipes that take an hour or less to make are rated higher than recipes that take longer to make.

## Framing a Prediction Problem

### Problem Identification

**Prediction Problem:**
We aim to predict the number of minutes that it would take to prepare a recipe.

**Prediction Type:**
Since time is a continuous quantitative variable, this would be a *regression* problem.

**Features Available at Time of Prediction:**
The features we would know before starting to cook a recipe would be: `'n_ingredients'`, `'n_steps'`, `'is_easy'`, `'calories'`, `'protein'`, `'saturated_fat'`.

**Response Variable:**
The response variable we plan to predict is the number of minutes that to prepare a recipe. We chose this variable because time is directly related to the characteristics of a recipe and is a practical thing someone might want to know before deciding whether or not to make a recipe. By being able to predict how long a recipe would take to make, people would be able to plan cooking time in their schedules accordingly.

**Metric of evaluation:**
We plan to use **$R^2$** as our metric of evaluation since it is a canonically standard evaluation metric of linear regression and will measure the quality of the linear fit, which helps determine how well our overall predictions are through the variance of data compared to the model.

## Baseline Model

### Model Description

For our baseline model, we build a simple linear regression model to predict the number of minutes it would take to cook a recipe. To remove extreme outliers, we filter out recipes that take more than 24 hours (1440 minutes) to prepare. We trained the model using 80% of the data and used the remaining 20% to test our model's accuracy.

We used the features `'n_steps'` and `'n_ingredients'` to train our model. Since both features are quantitative, no further encodings were necessary to compute.

To make our model, we use a `StandardScaler` to standardize our features so that we can better interpret our model's coefficients, then use the `LinearRegression` object to fit a simple model to predict preparation time.

### Results

After filtering our data, we are left with 83194 unique recipes.

**Training Results:**
* $R^2$ score: 0.0333

**Testing Results:**
* $R^2$ score: 0.0353

**Feature Coefficients:**
* `'n_steps'`: +11.76 minutes (per additional step)
* `'n_ingredients'`: +9.33 minutes (per additional ingredient)

### Analysis

Since our baseline model captures only about 3.33% of the variance in preparation time, our model performs poorly. However, our model provides insights such as:
* Establishing a minimum performance benchmark
* Confirming relationships (more ingredients/steps in a recipe correlates to a longer preparation time)
* Shows the need for a more complex model with the use of feature engineering to improve predictions 