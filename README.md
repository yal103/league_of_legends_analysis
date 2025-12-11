# League of Legends Vision Control Analysis

By: Yangyang Liu

DSC 80 - Fall 2025

## Introduction

League of Legends is a popular, free-to-play 5-vs-5 strategy game created by Riot Games in which two teams fight to destroy the enemy base (Nexus) while controlling objectives, managing resources, and coordinating map movements. Since the game is highly strategic and generates a lot of data on team performance (eg. kills, gold, vision control), professional matches are a natural source for quantitative analysis.

Oracle Elixir's 2024 League of Legends Esports Match Data contains player and match level statistics from professional League of Legends matches during the 2024 competitive season. Each row of the dataset summarizes a player's performance and team's performance in a single game, providing details on how team strategies impact game results.

There are many in-game factors of that predict whether a team wins a League of Legends match. Vision is one of the most strategically important, at least visible, mechanics in League of Legends. Vision control is often viewed as a win condition, but it's less obvious compared to kills or objective control. The central question we are interested in is: **To what extent does a team's vision control influence its likelihood of winning?**