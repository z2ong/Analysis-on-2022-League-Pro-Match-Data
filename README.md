## Introduction

## Data Cleaning and Exploratory Data Analysis
  I required very little cleaning for my dataset. I mostly focused on filtering the original dataframe for the information that I desired to work with. Since I wanted to focus specifically on tier 1 teams, I filtered the dataframe to only include data from the leagues LCK, LPL, CBLOL, LCP, LCS, and LEC. From there, I also only wanted the majority of game stats and overall team stats, so I filtered the dataframe further to only include team data as well. Beyond this, I filtered it down to the columns I wanted to analyze that I thought would answer my research question of game length, dependent on 15-minute stats and league.

|   golddiffat15 |   xpdiffat15 |   csdiffat15 | league   |   killsat15 |   deathsat15 |   gamelength |   team kpm | side   |
|---------------:|-------------:|-------------:|:---------|------------:|-------------:|-------------:|-----------:|:-------|
|            nan |          nan |          nan | LPL      |         nan |          nan |         1365 |     0.5714 | Blue   |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1365 |     0.2637 | Red    |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1444 |     0.9141 | Blue   |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1444 |     0.3324 | Red    |
|            nan |          nan |          nan | LPL      |         nan |          nan |         1893 |     0.3803 | Blue   |

As you can see, there is still a lot more data missing, so I began searching for what I could possibly do to impute the missing data. As I verified it, I found that all of the missing data was only connected to the league LPL, meaning that the LPL was the only league that contained missing data in this data frame. Because of how much missingness there was across rows, I decided to drop rows that were from the LPL league. 

The resulting data frame below has no missing values; thus, my cleaning has been mostly finished. Later, I realized that the diff stats every other row were duplicates of each other in magnitude, so as not to have such a 

|   golddiffat15 |   xpdiffat15 |   csdiffat15 | league   |   killsat15 |   deathsat15 |   gamelength | side   |
|---------------:|-------------:|-------------:|:---------|------------:|-------------:|-------------:|:-------|
|           4757 |         1540 |            1 | LCK      |           4 |            1 |         2195 | Blue   |
|          -4757 |        -1540 |           -1 | LCK      |           1 |            4 |         2195 | Red    |
|          -1045 |         -735 |          -18 | LCK      |           2 |            4 |         2070 | Blue   |
|           1045 |          735 |           18 | LCK      |           4 |            2 |         2070 | Red    |
|           1309 |         -170 |          -15 | LCK      |           3 |            1 |         2233 | Blue   |

## Assessment of Missingness

## Hypothesis Testing

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis
