# Cleaning the Epicurious Dataset

## Cleaning The Data

I will be working with the CSV file generously contributed to Kaggle here: [link to dataset](https://www.kaggle.com/hugodarwood/epirecipes/).

The CSV file consists of 20052 rows and 679 columns. Each row is a recipe on Epicurious and each column is a feature. Features include the rating of the recipe, categorical variables for the ingredients used, and also nutritional information (calories, protein, fat and sodium) for one individual serving.

Many of the recipes are missing nutritional information. For this project, I chose eliminate those rows, after which I am left with 15866 recipes.

<p align="left">
  <img src="https://raw.githubusercontent.com/tommyzakhoo/epicurious/master/calories_errors.png", width="600">
</p>

Using a simple plot, as shown above, I can see that the calories column is filled with probably erroneous outliers. The same is true for the other nutritional information columns. While it is clear that a single serving of a recipe having 200,000+ calories is probably an error, it is less clear what should be the threshold for a calories entry to be counted as erroneous. The same goes for the other nutritional information.

In the end, I chose to start by deleting the 77 rows with calories >= 5000, then deleting the 79 rows with sodium >= 10000. This leaves me with 15710 data points that appears to at least be free of obvious errors. Since we are working with a CSV file, all these cleaning are easily done in Excel. This cleaned dataset can be found here: [data_trimmed.csv](data_trimmed.csv).

### Preliminary

### Correlation Matrix and Multicollinearity

One last thing I did before running the logistic regression is to check for explanatory variables that are highly correlated to each other. This is one simplified way to reduce the effects of [multicollinearity](https://en.wikipedia.org/wiki/Multicollinearity). 

I do this by computing the correlation matrix in Python, disregarding the 1s in the diagonal entries, and then checking to see if the Pearson correlation coefficient of any pairs of variables crosses the arbitrary threshold of > 0.85 or < -0.85.

```Python
import pandas as pd

data = pd.read_csv('data_BLD.csv')
    
C = data.drop(columns = ['breakfast','lunch','dinner'])
C = C.corr()
C[C == 1.0] = 0.0 # disregard the 1s on diagonals

(C > 0.85).sum().sum() # see how many variable pairs meet our criteria
(C < -0.85).sum().sum()
```
6 pairs of variables were picked out by this simple criteria. All of them have > 0.85 correlation as shown by the figure below.

<p align="left">
  <img src="https://raw.githubusercontent.com/tommyzakhoo/epicurious/master/correlations.png", width="600">
</p>

A takeaway from this is that recipes that are high in fats are also high in calories! Portland is in Oregon, so the high correlation wasn't a surprise. Finally, it looks like most drinks recipes posted on Epicurious are non-alcoholic. I chose to drop the variables for fat, Portland and non-alcoholic.
