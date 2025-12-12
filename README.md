# League of Legends Vision Control Analysis

By: Yangyang Liu

DSC 80 - Fall 2025

## Introduction

League of Legends is a popular, free-to-play 5-vs-5 strategy game created by Riot Games in which two teams fight to destroy the enemy base (Nexus) while controlling objectives, managing resources, and coordinating map movements. Since the game is highly strategic and generates a lot of data on team performance (eg. kills, gold, vision control), professional matches are a natural source for quantitative analysis.

Oracle Elixir's 2024 League of Legends Esports Match Data contains player and match level statistics from professional League of Legends matches during the 2024 competitive season. Each row of the dataset summarizes a player's performance and team's performance in a single game, providing details on how team strategies impact game results.

There are many in-game factors of that predict whether a team wins a League of Legends match. Vision is one of the most strategically important, at least visible, mechanics in League of Legends. Vision control is often viewed as a win condition, but it's less obvious compared to kills or objective control. The central question we are interested in is: **To what extent does a team's vision control influence its likelihood of winning?**

The dataset contains 117648 rows and 164 columns of match data. A select few of the columns will be particularly useful for our analysis.

| Column| Description | 
| :------ | :------ | 
|`gameid`| The unique identifier of a game.
| `position`     | The position of the player (either `top`, `jng`, `mid`, `bot`, `sup`). If the row corresponds to team data, the value is `team`.   | 
| `side`  | The side in which a player/team plays during the match, either `Blue` or `Red`.  | 
| `gamelength`| The duration of the match (in seconds).|
| `result`| The outcome of the match for the player/team, `0` for a loss and `1` for a win.|
|`wardsplaced`| The number of vision wards a player/team placed during the match.| 
|`wardskilled`| The number of enemy wards a player/team clears during the match.|
|`controlwardsbought`| The number of control wards purchased by a player/team during the match.|
|`visionscore`| League of Legends's in-game metric for a player/team's total vision impact.

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning
#### 1. Keeping Only Team Rows

In our original dataset, each `gameid` corresponds to up to 12 rows – one for each of the 10 players in each match and 2 for the match summary data for each of the two teams. Since our overall question primarily focuses on the general metrics of a team rather than individual players, we will only keep the rows that correspond to teams. Since `top`, `jng` (jungle), `mid`, `bot`, and `sup` (support) refer to all and the only roles in League of Legends and since there are now rows where the position entry is missing, a row correspond to a team' match data if and only if the value of `position` for that row is `team`.

#### 2. Keeping Only Relevant Columns

We keep only the column that directly needed for analysis: `gameid`, `position`, `side`, `gamelength`, `result`, `wardsplaced`, `wardskilled`, `controlwardsbought`, `visionscore`. Narrowing the dataset reduces noise and prevents our future models from learning from irrelevant features.

#### 3. Dropping Games with Unclear Results
Very rarely (twice), a match ends with no winner, indicated by a `0` in the `result` column for both teams. Since these games misrepresent losses as real game outcomes and have little significance in our large dataset, we can drop the corresponding rows.


#### 4. Normalizing Vision Metrics by Game Length

Normalizing each of the vision metrics by gme length is very important because longer matches naturally result in higher numbers of vision wards being placed, killed and bought, hence a larger overall team vision score. Per-minute metrics give us a clearer picture of a team's overall vision 'efficiency'. We create the following per-minute versions of each variable: `wardsplaced_per_min`, `wardskilled_per_min`, `controlwardsbought_per_min`, `visionscore_per_min` by dividing each metric by `gamelength_min = gamelength / 60`.

There is no need to canonicalize the strings as all of the strings are already 'cleaned', in the sense that each of the names of the leagues are equal to each other when capitalization is not in consideration. There is also no need to canonicalize the strings in `side`. The team's side is either `Blue` or `Red`.
The first few rows of our cleaned DataFrame as shown below:

| gameid             | side   |   gamelength |   result |   wardsplaced |   wardskilled |   controlwardsbought |   visionscore |   gamelength_min |   wardsplaced_per_min |   wardskilled_per_min |   controlwardsbought_per_min |   visionscore_per_min |
|:-------------------|:-------|-------------:|---------:|--------------:|--------------:|---------------------:|--------------:|-----------------:|----------------------:|----------------------:|-----------------------------:|----------------------:|
| 10660-10660_game_1 | Blue   |         1886 |        0 |            97 |            59 |                   33 |           250 |          31.4333 |               3.0859  |               1.87699 |                      1.04984 |               7.95334 |
| 10660-10660_game_1 | Red    |         1886 |        1 |           122 |            49 |                   56 |           277 |          31.4333 |               3.88123 |               1.55885 |                      1.78155 |               8.8123  |
| 10660-10660_game_2 | Blue   |         1911 |        0 |            88 |            47 |                   39 |           236 |          31.85   |               2.76295 |               1.47567 |                      1.22449 |               7.40973 |
| 10660-10660_game_2 | Red    |         1911 |        1 |           116 |            56 |                   49 |           314 |          31.85   |               3.64207 |               1.75824 |                      1.53846 |               9.85871 |
| 10660-10660_game_3 | Blue   |         1324 |        1 |            60 |            35 |                   23 |           162 |          22.0667 |               2.71903 |               1.5861  |                      1.0423  |               7.34139 |

### Univariate Analysis
To understand the general behavior of vision control metrics in competitive League of Legends matches, we first need a glance at how each of the features are distributed. We plot and analyze the distributions of each quantitative variable using histograms. For example, the distribution of game lengths (`gamelength`) and wards placed (`wardsplaced`) are shown below:
<iframe
  src="assets/gamelength.html"
  width="800"
  frameborder="0"
></iframe>

<iframe
  src="assets/wardsplaced.html"
  width="800"
  frameborder="0"
></iframe>

Along with the other raw vision metrics, we can notice that the distribution is slightly skewed to the right. This is likely due to the fact that longer matches naturally accumulate more vision events as we can notice that the shape of the distributions of the raw vision metrics are similar to the shape of the game length.

Raw totals are not good measures for analyses related to the outcome of a game. For example, a team who played for 45 minutes may appear to have much more vision control than a team who played for only 25 minutes even if the latter performed 'vision events' at a much faster rate.

Therefore, to correctly measure a team's vision control effectiveness, we need to account for the length of each game, hence our need for normalized columns like `wardsplaced_per_min` and `visionscore_per_min`. Below is the distribution of the `wardsplaced_per_min`:

<iframe
  src="assets/wardsplaced_per_min.html"
  width="800"
  frameborder="0"
></iframe>

Comparing this with our original distributions, we can see that the distribution of our normalized vision quantities are now approximately Normal, which is more suited for analyses.


## Assessment of Missingness


## Hypothesis Testing


## Framing a Prediction Problem


## Baseline Model


## Final Model


## Fairness Analysis