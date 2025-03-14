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