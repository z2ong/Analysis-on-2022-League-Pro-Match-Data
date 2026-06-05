# Analysis On 2022 League Pro Match Data

## Introduction

## Data Cleaning and Exploratory Data Analysis
  I required very little cleaning for my dataset. I mostly focused on filtering the original dataframe for the information that I desired to work with. Since I wanted to focus specifically on tier 1 teams, I filtered the dataframe to only include data from the leagues LCK, LPL, CBLOL, LCS, and LEC. From there, I also only wanted the majority of game stats and overall team stats, so I filtered the dataframe further to only include team data as well. Beyond this, I filtered it down to the columns I wanted to analyze that I thought would answer my research question of predicting game length and if different leagues had differing game lengths based on regional metas, dependent on 15-minute stats.

<br><br>

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

<br><br>

<iframe
  src="assets/gamelength-across-leagues-histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<br><br>

<iframe
  src="assets/relationship-gamelength-golddiffat15.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<br><br>

<iframe
  src="assets/relationship-gamelength-deathsat15.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Assessment of Missingness

## Hypothesis Testing

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis
