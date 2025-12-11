# League of Legends Vision Control Analysis

By: Yangyang Liu

DSC 80 - Fall 2025

## Introduction

League of Legends is a popular, free-to-play 5-vs-5 strategy game created by Riot Games in which two teams fight to destroy the enemy base (Nexus) while controlling objectives, managing resources, and coordinating map movements. Since the game is highly strategic and generates a lot of data on team performance (eg. kills, gold, vision control), professional matches are a natural source for quantitative analysis.

Oracle Elixir's 2024 League of Legends Esports Match Data contains player and match level statistics from professional League of Legends matches during the 2024 competitive season. Each row of the dataset summarizes a player's performance and team's performance in a single game, providing details on how team strategies impact game results.

There are many in-game factors of that predict whether a team wins a League of Legends match. Vision is one of the most strategically important, at least visible, mechanics in League of Legends. Vision control is often viewed as a win condition, but it's less obvious compared to kills or objective control. The central question we are interested in is: **To what extent does a team's vision control influence its likelihood of winning?**

The dataset contains 117648 rows and 164 columns of match data. A select few of the columns will be particularly useful for our analysis.
'top' 'jng' 'mid' 'bot' 'sup' 'team'

| Column| Description | 
| :------ | :------ | 
| `position`     | The position of the player (either `top`, `jng`, `mid`, `bot`, `sup`). If the row corresponds to team data, the value is `team`.   | 
| `side`  | The side in which a player/team plays during the match, either `Blue` or `Red`.  | 
| `gamelength`| The duration of the match (in seconds).|
| `result`| The outcome of the match for the player/team, `0` for a loss and `1` for a win.|
|`wardsplaced`| The number of vision wards a player/team placed during the match.| 
|`wardskilled`| The number of enemy wards a player/team clears during the match.|
|`controlwardsbought`| The number of control wards purchased by a player/team during the match.|
|`visionscore`| League of Legends's in-game metric for a player/team's total vision impact.