# Analysis On 2022 League Pro Match Data

## Introduction
The dataset I am using for this project is 2022 esports match data from the website OraclesElixir. My project's main focus is: Can early-game metrics at the 15-minute mark predict the total duration of professional tier 1 League of Legends matches? I found this question semi-important because if you enjoy watching League of Legends pro matches, specifically tier 1, and you only want to view the game live but possibly have a commitment coming up, you can utilize the 15-minute stats to glance ahead at whether you have time to watch the rest of the match or not, getting a prediction instead of just guessing. Aside from the trivial uses of this project, the whole dataset itself contains 150348 rows and 165 columns. The main columns I will be focusing on are golddiffat15, xpdiffat15, csdiffat15, killsat15, deathsat15, league, and gamelength, with a slight focus on the column side and position for further filtering of the dataframe. For reference, I only wanted to make predictions about teams, so I filtered the data frame to include only team data. golddiffat15 is the difference in gold between teams for those specific matches. They are identical in magnitude across games, but negative or positive depending on which team held the lead. xpdiffat15 is similar to that of golddiffat15, but instead of being a difference in gold between teams, it highlights the difference in experience level. csdiffat15 follows a similar trend, but highlights a difference in the creep score or the number of minions killed across the whole team. killsat15 highlights the number of kills a team made, while deathsat15 indicates the number of deaths they had. league is the different League of Legends regional organizations, where the matches were held. And finally, gamelength is just the duration of that particular match, which is identical between games. I should explain that when filtered to only have team data, each row indicates one team, and below or above it, the team they faced. There could be duplicate values between them, so as to avoid overinflating my model with similar values, I did some filtering between sides to get rid of duplicity.


## Data Cleaning and Exploratory Data Analysis
I required very little cleaning for my dataset. I mostly focused on filtering the original dataframe for the information that I desired to work with. Since I wanted to focus specifically on tier 1 teams, I filtered the dataframe to only include data from the leagues LCK, LPL, CBLOL, LCS, and LEC. From there, I also only wanted the majority of game stats and overall team stats, so I filtered the dataframe further to only include team data as well. Beyond this, I filtered it down to the columns I wanted to analyze that I thought would answer my research question of predicting game length and if different leagues had differing game lengths based on regional metas, dependent on 15-minute stats.

<br><br>
### Slightly Filtered DataFrame

|   golddiffat15 |   xpdiffat15 |   csdiffat15 | league   |   killsat15 |   deathsat15 |   gamelength |   team kpm | side   |
|---------------:|-------------:|-------------:|:---------|------------:|-------------:|-------------:|-----------:|:-------|
|            nan |          nan |          nan | LPL      |         nan |          nan |         1365 |     0.5714 | Blue   |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1365 |     0.2637 | Red    |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1444 |     0.9141 | Blue   |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1444 |     0.3324 | Red    |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1893 |     0.3803 | Blue   |

<br><br>

As you can see, there is still a lot more data missing, so I began searching for what I could possibly do to impute the missing data. As I verified it, I found that all of the missing data was only associated with the league LPL, meaning that the LPL was the only league that contained missing data in this data frame. Because of how much missingness there was across rows, I decided to drop rows that were from the LPL league entirely. 

<br><br>
### Dropped LPL DataFrame

|   golddiffat15 |   xpdiffat15 |   csdiffat15 | league   |   killsat15 |   deathsat15 |   gamelength | side   |
|---------------:|-------------:|-------------:|:---------|------------:|-------------:|-------------:|:-------|
|           4757 |         1540 |            1 | LCK      |           4 |            1 |         2195 | Blue   |
|          -4757 |        -1540 |           -1 | LCK      |           1 |            4 |         2195 | Red    |
|          -1045 |         -735 |          -18 | LCK      |           2 |            4 |         2070 | Blue   |
|           1045 |          735 |           18 | LCK      |           4 |            2 |         2070 | Red    |
|           1309 |         -170 |          -15 | LCK      |           3 |            1 |         2233 | Blue   |

<br><br>

The resulting data frame above has no missing values; thus, my cleaning has been mostly finished. Later, I also realized that the diff stats every other row were duplicates of each other in magnitude, so as not to have such redundant values in the model and overinflate it with these particular values, I decided to filter only to one side per match since the differentials are equal in magnitude.

<br><br>

### Let us move on to EDA:

<iframe
  src="assets/golddiff15.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Above is a histogram highlighting the distribution of the golddiffat15 column. This histogram highlights that there is an approximately normal distribution for gold differences across many of the professional tier 1 games that occurred in 2022. This could indicate that at the 15-minute mark, most teams are equally balanced for the most part in terms of gold, with fewer cases of early leads. While we can't conclude exactly how long the game might take, this could be an indicator of early snowballing for particular matches, leading to faster wins/losses and shorter game times for the cases where golddiff is further from 0.

<br><br>

<iframe
  src="assets/deathsat15.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Above is another histogram, but this time we are highlighting the distribution of the deathsat15 column. This histogram shows a right-skewed pattern with most of the values centered around 2. There are no negative values for deaths since it's only starting from 0. It appears that, for the most part, teams on average have 2 deaths by the 15-minute mark, with some spanning upwards of 14. We might be able to venture that the games where there are upwards of 14 deaths within 15 minutes could indicate a faster-paced game, while the games with fewer deaths by the 15-minute mark could be slower. 

<br><br>

<iframe
  src="assets/gamelength-boxplot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Here, I wanted to observe the distribution of gamelength across leagues to note if there was a drastic difference that could highlight different metas. I want my model to be accurate to the individual metas of particular regions/leagues, so I made a box plot to compare the differences across distributions visually.

<br><br>

<iframe
  src="assets/gamelength-across-leagues-histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I did the same thing above, by generating a histogram, but this time overlaying the distributions on top of each other. While the boxplot highlighted the general trend, the histogram allowed me to note more subtle nuances, like how the LCK league's left skew did have considerable points, but it was more centered below 2000 seconds. I wanted to see if there was any visual difference so that I could confirm my hypothesis tests visually.

<br><br>

<iframe
  src="assets/relationship-gamelength-golddiffat15.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

For this scatterplot, I wanted to note whether the trends with gamelength were linear or non-linear. This led me to choose a nonlinear model because of the data's shape.

<br><br>
### Pivot Table Comparing The Relationship of golddiffat15 and gamelength Across Different Leagues

| league   |   gamelength |   golddiffat15 |
|:---------|-------------:|---------------:|
| CBLOL    |      1974.09 |       41.572   |
| LCK      |      2020.06 |       -4.31906 |
| LCS      |      1981.59 |      226.157   |
| LEC      |      1993.26 |      318.465   |


For the pivot table above, I was hoping to use it to analyze whether or not different trends occurred in different leagues, comparing the average gold difference at 15 minutes into the game and game length averages. While I can't conclude a particular relationship, the LCK does show that the less of a gold difference there is, the longer the game might take. The other leagues also verify this, showing shorter game length averages with higher gold differences, but the trend doesn't appear to be linear.

<br><br>

## Assessment of Missingness

In the original dataframe, I believed there were columns of data that were NMAR. Specific to this assessment, however, I will only be analyzing golddiffat15, but it is important to note that many other columns shared the situation that golddiffat15 was under. As I was enacting EDA on the dataset, I noticed that there were rows and rows of NaN values. To sleuth, I first thought to compare leagues, as I had noted the change when I had filtered to only include tier 1 leagues. I looped through the columns I wanted to work with, checking which league had missingness. I found only rows that were a part of LPL matches had missing values so I wanted to utilize this section to do more sleuthing. 

Below is the table I generated to observe the percentage of missing values across different leagues.
### Leagues and Missingness

| league   |   False |   True |
|:---------|--------:|-------:|
| CBLOL    |       1 |      0 |
| LCK      |       1 |      0 |
| LCS      |       1 |      0 |
| LEC      |       1 |      0 |
| LPL      |       0 |      1 |

<iframe
  src="assets/missingness-across-leagues.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Above is a histogram of missingness; all other leagues have no missingness, while LPL is completely missing all values for golddiffat15 specifically, but through other checks, I found it was similar for columns csdiffat15, xpdiffat15, killsat15, and deathsat15. I am only showing golddiffat15, since it was the column I used to check specifically for NMAR data.

To test if the missingness of golddiffat15 was independent of league, to see if the data really was NMAR or MAR, I did a permutation test comparing the missing values of golddiffat15 with the categories of league. Through the test, I obtained a p-value < 0.001. This led me to conclude that there was significant evidence that the missingness of golddiffat15 is dependent on the variable league 

## Hypothesis Testing

For this section, I wanted to note if different leagues had different game length distributions. This would mean, if I rejected the null, that including the league as a possible predictor would give my model a bit more predictive power. Adding league as a predictor would still add to the model's predictive power if I did include it, but there would be more meaning to include it as a feature if, when I perform the permutation test, I reject the null and conclude that different leagues could have different game length distributions.

### For one of the permutation tests I did:

My null and alternative hypotheses were:
>H_0: Is the distribution of gamelength for LCK the same as the distribution of gamelength for LCS.
>
>H_A: Is there a significant difference between the mean gamelength for LCK and LCS.
>
>I am using the observed mean absolute difference in gamelength between LCK and LCS to determine whether my null or alternative hypothesis is true. I set my a = 0.05 to ensure 95% confidence, but as this is a two-tailed test, I used a threshold of a / 2 = 0.025.

When performing the hypothesis test, I obtained a p-value = 0.034. Since my p-value = 0.034 was greater than a/2 = 0.025, I don't have enough evidence to conclude that there is a difference in the mean game length between LCK and LCS matches. This signals that the predictor league might not be a very powerful feature in my future model, but it can still be useful for improving my model's generalizability to nonlinear patterns between regions and game length.

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis
