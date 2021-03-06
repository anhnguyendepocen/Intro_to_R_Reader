# R Functions and Workflow Exercises

## Do the following:
1. What class is the vector ```c(1, TRUE, 3)```? Why is it not a character vector?
2. Make and print this tibble. What do the abbreviations under each column name mean?

```r
tibble(x = c(1, 2, 3),
       y = c("A", "B", "C"),
       z = c(TRUE, FALSE, TRUE))
```
3. Inspect the ```smiths``` dataset (loaded with the tidyverse - you can access it like ```iris```). How can you fix those missing values?

## Work with other datasets:
1. ```spread()``` the ```iris``` dataset so that each species' petal width is in its own column. Then ```gather()``` the table back together. What's different about this dataframe?
2. Select all the rows of ```iris``` where the species is setosa. Now select all the rows where the species _isn't_ setosa.
3. What's the mean price for each cut of diamond in the ```diamonds``` dataset? 
 
