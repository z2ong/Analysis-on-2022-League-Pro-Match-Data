# Analysis On 2022 League Pro Match Data

## Introduction
The dataset I am using for this project is 2022 esports match data from the website OraclesElixir. 

My project's main focus is: ***Can early-game metrics at the 15-minute mark somewhat accurately predict the total duration of professional Tier 1 League of Legends matches, and do different league metas influence such game lengths?*** 

I found this question semi-important because if you enjoy watching League of Legends pro matches, specifically Tier 1, and you only want to view the game live but possibly have a commitment coming up, you can utilize the 15-minute stats to glance ahead at whether you have time to watch the rest of the match or not, getting a prediction instead of just guessing. I wanted to be a bit accurate in my prediction, so I wanted to verify if different leagues, as they might have different playstyles, could have different game length distributions. I thought that possibly including them in my prediction might improve the generalizability of my model and improve predictive power. 

Aside from the trivial uses of this project, the whole dataset itself contains 150348 rows and 165 columns. The main columns I will be focusing on are golddiffat15, xpdiffat15, csdiffat15, killsat15, deathsat15, league, and gamelength, with a slight focus on the column side and position for further filtering of the dataframe. For reference, I only wanted to make predictions about teams, so I filtered the data frame to include only team data. 

- golddiffat15: is the difference in gold between teams for those specific matches. They are identical in magnitude across the specific match, but negative or positive depending on which team held the lead.
- xpdiffat15: is similar to that of golddiffat15, but instead of being a difference in gold between teams, it highlights the difference in experience level.
- csdiffat15: follows a similar trend, but highlights a difference in the creep score or the number of minions killed across the whole team.
- killsat15: highlights the number of kills a team made
- deathsat15: indicates the number of deaths a team had.
- league: is the different League of Legends regional organizations, where the matches were held.
- And finally, gamelength: is just the duration of that particular match (in seconds), which is identical between specific matches.

I should explain that when filtered to only have team data, each row indicates one team, and below or above it, the team they faced during a specific match. There could be duplicate values between them, so as to avoid overinflating my model with similar values, I did some filtering utilizing the sides column to get rid of duplicity in future steps.

<br><br><br>

## Data Cleaning and Exploratory Data Analysis
I required very little cleaning for my dataset. I mostly focused on filtering the original dataframe for the information that I desired to work with. Since I wanted to focus specifically on tier 1 teams, I filtered the dataframe to only include data from the leagues LCK, LPL, CBLOL, LCS, and LEC. From there, I also only wanted the majority of game stats and overall team stats, so I filtered the dataframe further to only include team data as well. Beyond this, I filtered it down to the columns I wanted to analyze that I thought would answer my research question of predicting game length and if different leagues had differing game lengths based on regional metas, dependent on 15-minute stats.

<br>

### Slightly Filtered DataFrame

<br>

|   golddiffat15 |   xpdiffat15 |   csdiffat15 | league   |   killsat15 |   deathsat15 |   gamelength |   team kpm | side   |
|---------------:|-------------:|-------------:|:---------|------------:|-------------:|-------------:|-----------:|:-------|
|            nan |          nan |          nan | LPL      |         nan |          nan |         1365 |     0.5714 | Blue   |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1365 |     0.2637 | Red    |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1444 |     0.9141 | Blue   |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1444 |     0.3324 | Red    |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1893 |     0.3803 | Blue   |

<br>

As you can see, there is still a lot more data missing, so I began searching for what I could possibly do to impute the missing data. As I verified it, I found that all of the missing data was only associated with the league LPL, meaning that the LPL was the only league that contained missing data in this data frame. Because of how much missingness there was across rows, I decided to drop rows that were from the LPL league entirely. 

<br>
### Dropped LPL DataFrame

<br>

|   golddiffat15 |   xpdiffat15 |   csdiffat15 | league   |   killsat15 |   deathsat15 |   gamelength | side   |
|---------------:|-------------:|-------------:|:---------|------------:|-------------:|-------------:|:-------|
|           4757 |         1540 |            1 | LCK      |           4 |            1 |         2195 | Blue   |
|          -4757 |        -1540 |           -1 | LCK      |           1 |            4 |         2195 | Red    |
|          -1045 |         -735 |          -18 | LCK      |           2 |            4 |         2070 | Blue   |
|           1045 |          735 |           18 | LCK      |           4 |            2 |         2070 | Red    |
|           1309 |         -170 |          -15 | LCK      |           3 |            1 |         2233 | Blue   |

<br>

The resulting data frame above has no missing values; thus, my cleaning has been mostly finished. Later, I also realized that the diff stats every other row were duplicates of each other in magnitude, so as not to have such redundant values in the model and overinflate it with these particular values, I decided to filter only to one side per match since the differentials are equal in magnitude.

<br>

### Let us move on to EDA:

<iframe
  src="assets/golddiff15.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Above is a histogram highlighting the distribution of the golddiffat15 column. This histogram highlights that there is an approximately normal distribution for gold differences across many of the professional tier 1 games that occurred in 2022. This could indicate that at the 15-minute mark, most teams are equally balanced for the most part in terms of gold, with fewer cases of early leads. While we can't conclude exactly how long the game might take, this could be an indicator of early snowballing for particular matches, leading to faster wins/losses and shorter game times for the cases where golddiff is further from 0.

<br>

<iframe
  src="assets/deathsat15.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Above is another histogram, but this time we are highlighting the distribution of the deathsat15 column. This histogram shows a right-skewed pattern with most of the values centered around 2. There are no negative values for deaths since it's only starting from 0. It appears that, for the most part, teams on average have 2 deaths by the 15-minute mark, with some spanning upwards of 14. We might be able to venture that the games where there are upwards of 14 deaths within 15 minutes could indicate a faster-paced game, while the games with fewer deaths by the 15-minute mark could be slower. 

<br>

<iframe
  src="assets/gamelength-boxplot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Here, I wanted to observe the distribution of gamelength across leagues to note if there was a drastic difference that could highlight different metas. I want my model to be accurate to the individual metas of particular regions/leagues, so I made a box plot to compare the differences across distributions visually.

<br>

<iframe
  src="assets/gamelength-across-leagues-histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I did the same thing above, by generating a histogram, but this time overlaying the distributions on top of each other. While the boxplot highlighted the general trend, the histogram allowed me to note more subtle nuances, like how the LCK league's left skew did have considerable points, but it was more centered below 2000 seconds. I wanted to see if there was any visual difference so that I could confirm my hypothesis tests visually.

<br>

<iframe
  src="assets/relationship-gamelength-golddiffat15.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

For this scatterplot, I wanted to note whether the trends with gamelength were linear or non-linear. This led me to choose a nonlinear model because of the data's shape.

<br>

### Pivot Table Comparing The Relationship of golddiffat15 and gamelength Across Different Leagues

<br>

| league   |   gamelength |   golddiffat15 |
|:---------|-------------:|---------------:|
| CBLOL    |      1974.09 |       41.572   |
| LCK      |      2020.06 |       -4.31906 |
| LCS      |      1981.59 |      226.157   |
| LEC      |      1993.26 |      318.465   |


For the pivot table above, I was hoping to use it to analyze whether or not different trends occurred in different leagues, comparing the average gold difference at 15 minutes into the game and game length averages. While I can't conclude a particular relationship, the LCK does show that the less of a gold difference there is, the longer the game might take. The other leagues also verify this, showing shorter game length averages with higher gold differences, but the trend doesn't appear to be linear or very apparent.

<br><br><br>

## Assessment of Missingness

In the original dataframe, I believed there were columns of data that were NMAR. Specific to this assessment, however, I will only be analyzing golddiffat15. I thought that it was not NMAR. It is important to note that not all other columns were similar to golddiffat15, but the ones specific to my prediction problem were. As I was enacting EDA on the dataset, I noticed that there were rows and rows of NaN values. To sleuth, I first thought to compare leagues, as I had noted an abrupt wall of NAN values when I had filtered to only include Tier 1 leagues. I looped through the columns I wanted to work with, checking which league had missingness. I found that only rows that were a part of LPL matches had missing values, so I wanted to utilize this section to verify my thoughts. 

To test if the missingness of golddiffat15 was independent of league, to see if the data really was NMAR or MAR, I did a permutation test comparing the missing values of golddiffat15 with the categories of league. Through the test, I obtained a p-value < 0.001. Since my p-value was less than the a = 0.05, I had enough evidence to conclude that the missingness of golddiffat15 was dependent on the variable league. This means that the data golddiffat15 is MAR and not NMAR.

<iframe
  src="assets/permuted-missingness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Above is the empirical distribution of the permuted TVDs compared to the observed test statistic, highlighting the relationship between golddiffat15 missingness and league shuffled. Since the observed statistic is so far from the permuted data, we visually confirm that the missingness of golddiffat15 is not random, but is statistically dependent on the league variable.

<br><br><br>

## Hypothesis Testing

For this section, I wanted to note if different leagues had different game length distributions. This would mean, if I rejected the null, that including the league as a possible predictor would give my model a bit more predictive power. Adding league as a predictor could still add to the model's predictive power if I did include it, but there would be more meaning to include it as a feature if, when I perform the permutation test, I found that I reject the null and conclude that different leagues could have different game length distributions.

My null and alternative hypotheses were:

<br>

**H_0: Is the distribution of gamelength for LCK the same as the distribution of gamelength for LCS.**

**H_A: Is there a significant difference between the mean gamelength for LCK and LCS.**

I am using the observed *mean absolute difference* in gamelength between LCK and LCS to determine whether my null or alternative hypothesis is true. I set my *a = 0.05* to ensure 95% confidence, but as this is a two-tailed test, I used a threshold of *a / 2 = 0.025*.

When performing the hypothesis test, I obtained a *p-value = 0.034*. Since my p-value = 0.034 was greater than *a / 2 = 0.025*, I didn't have enough evidence to conclude that there is a difference in the mean game length between LCK and LCS matches. This signaled that the predictor league might not be a very powerful feature in my future model to represent patterns between these leagues, but I wanted to attempt to try it to see if it would still be useful for improving my model's generalizability to nonlinear patterns between leagues and game length, at least in a smaller fashion.

<br><br><br>

## Framing a Prediction Problem

My prediction problem is:
> **How long would a league match take using information available 15 minutes into the match?**

This is a regression problem, as I am trying to predict the continuous numerical value of game length (seconds). My response variable is gamelength, which I thought was a good metric, as I want to predict how long a game might take. The metric I am choosing to evaluate my model is mean absolute error. I chose this metric because I figured there would be really large disparities in game lengths, and rather than penalizing outliers, by using a metric like residual mean squared error, which squares the error, I wanted to preserve my model's generalizability to ensure predictive accuracy across game durations.

I restricted the features I utilized for prediction to data that was only available within the first 15 minutes of the game and excluded features like damage per minute or team kills per minute, which would be calculated after the match and could have unintended data leakage concerning information not available within the first 15 minutes of the game. To add to this, I filtered the dataset to only include data from the red-sided teams, since features like xpdiffat15, golddiffat15, and csdiffat15 would have redundant observations across the same match, as the two teams would be equal in magnitude for these values. I also thought that since one team's kill stat and the other team's death stat would be equal, it would be redundant to include both as well. And finally, the gamelength and league would be identical for each match, so I believed that overall, only including one side, red or blue, in the model wouldn't be harmful to my model's predictive power.  

<br><br><br>

## Baseline Model

I fit two baseline models. I wanted to see how including or excluding league as a predictor would fare for my baseline model. To pick my baseline predictors, I chose two features most correlated with gamelength.

<br>

|              |   golddiffat15 |   xpdiffat15 |   csdiffat15 |   killsat15 |   deathsat15 |   gamelength |
|:-------------|---------------:|-------------:|-------------:|------------:|-------------:|-------------:|
| golddiffat15 |     1          |    0.777251  |    0.611267  |    0.486282 |    -0.480762 |  -0.00037917 |
| xpdiffat15   |     0.777251   |    1         |    0.666589  |    0.423114 |    -0.452702 |   0.0123029  |
| csdiffat15   |     0.611267   |    0.666589  |    1         |    0.164557 |    -0.152292 |   0.0233446  |
| killsat15    |     0.486282   |    0.423114  |    0.164557  |    1        |     0.244197 |  -0.156022   |
| deathsat15   |    -0.480762   |   -0.452702  |   -0.152292  |    0.244197 |     1        |  -0.147307   |
| gamelength   |    -0.00037917 |    0.0123029 |    0.0233446 |   -0.156022 |    -0.147307 |   1          |

<br>

In this case, the most correlated were killsat15 and deathsat15.

For the first baseline model, I fit a default Random Forest Regressor model. I fit the **discrete quantitative** features killsat15 and deathsat15 as is. In doing so, I obtained a train mean absolute error rate of about 259.167 seconds and a test mean absolute error rate of about 260.404 seconds. This showed promise as the training and testing error rates were similar, indicating that I didn't necessarily overfit the model.

For the second baseline model, I fit another default Random Forest Regressor model. This time, I fit the **discrete qualitative** features killsat15 and deathsat15 as is, but fit the **nominal qualitative** feature league using one-hot encoding. For this model, I obtained a train mean absolute error rate of about 234.875 seconds and a test mean absolute error rate of about 283.649 seconds. This increase in test error, but decrease in training error, showed beginning signs of overfitting my training data, thus leading me to believe that including league as a feature would do more harm to my final predictive model than good.

With this newfound information, I concluded that noting the differing leagues doesn't necessarily help in predicting game length for my model, thus leading me to exclude the feature league from my final model.

<br><br><br>

## Final Model

For my final model, I added the continuous quantitative features golddiffat15, xpdiffat15, and csdiffat15 and engineered them to obtain the magnitudes instead of their original differences by transforming them to their absolute values. While the direction could matter for other models and predictions, I believed it didn't directly impact mine since the magnitude of a lead in a match is what influences a game's length. Plus, the model could have viewed a negative or positive value differently, even though they are fundamentally the same, as I only cared how one-sided these particular stats were. These values weren't highly correlated with gamelength, and were even somewhat correlated with the previous predictors of my baseline model; however, I believed that including them would provide a more holistic representation of the game state at 15 minutes and capture nuances that a model purely containing the features deathsat15 and killsat15 didn't. In the actual game of League of Legends, small leads in resources, calculated by the values of cs, gold, and xp, can determine wins or losses, emphasizing how much of a lead one team would have over another, thus influencing the game duration. For this subtle reasoning alone, I believed that including these predictors might decrease the final model's mean absolute error rate, at least by a small amount, if any at all. 

For my model, I chose to utilize the Random Forest Regressor. I chose this model since, when performing EDA, I found the relationships between gamelength and the features golddiffat15, xpdiffat15, csdiffat15, killsat15, and deathsat15 were nonlinear. I also knew that the Random Forest Regressor was better at fitting to more complex relationships between predictors needing less feature engineering, thus influencing my decision to utilize it as my final model. I also just wanted to try working with a model I hadn't really worked with before. 

Random Forest Regressor fits T trees, bagging them using a random subset of features for each split, predicting different game lengths for different classifications of the game state at 15 minutes. The algorithm makes predictions by calculating the mean of the predicted value of all T trees. In my case, through cross-validation utilizing GridCV with a default of 5 folds, the best parameters for my Random Forest Regressor model were found to be 25 trees with depths of 5, meaning that all 25 trees calculated a predicted game length and were averaged to find the final prediction. Having a small enough depth to not overfit the data, and a large enough number of n_estimator trees to capture patterns in the data, but not too many to increase variance drastically and create wild predictions for particular patterns.

To sum it all up, my final model obtained a mean train absolute error rate of about 246.754 seconds and a mean test absolute error rate of about 257.353 seconds. This performed better than my baseline model, at least a bit, highlighting that including these more subtle features of resource gain provides a more reliable prediction of game length. I would say that you can utilize this model to accurately predict the length of a League of Legends game, given the 15-minute metrics golddiffat15, csdiffat15, xpdiffat15, killsat15, and deathsat15, with errors of about plus or minus 257.353 seconds. So, if you wanted to know how much time you had to watch a Tier 1 League of Legends match, you could use that risk to guesstimate how much time you had before rushing off to your next event.

Overall, I would conclude that early-game metrics at the 15-minute mark can somewhat accurately predict the total duration of professional tier 1 League of Legends matches, and that different league metas don't necessarily influence such game lengths, at least when accounting for early-game metrics such as golddiffat15, csdiffat15, xpdiffat15, killsat15, and deathsat15.

<br><br><br>

## Fairness Analysis

For performing the fairness assessment, I compared the leagues CBLOL and the LCS. When comparing their mean RMSE, they had the greatest difference so I thought to test them since they would probably be the best to determine how fair my model was. Since I have a regression model, I used RMSE as my evaluation metric.

My null and alternative hypotheses were:

<br>

**H_0: My model is fair. Its root mean square error for the leagues CBLOL and LCS are roughly the same, and any differences are due to random chance.**

**H_A: My model is unfair. Its root mean square error for the CBLOL is different than its root mean square error for the LCS match game length predictions.**

My test statistic was the absolute difference in RMSE between the CBLOL and LCS leagues. I set my *a = 0.05* to ensure 95% confidence, but as this is a two-tailed test, I used a threshold of *a / 2 = 0.025*.

From the permutation test, I obtained a p-value = 0.211. Since my p-value = 0.211 was greater than *a / 2 = 0.025*, I don't have enough evidence to conclude that my model is unfair and that the RMSE for CBLOL is different from the RMSE for the LCS match game length.
