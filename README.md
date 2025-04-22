# Introduction
For the final project, I’m exploring NBA basketball data collected from [Basketball Reference](https://www.basketball-reference.com/), a comprehensive database that tracks detailed player and game statistics. Basetball Reference contains an enourmous amount of data so in order to make my analyis more manageable, I constructed a custom dataset specifically designed to support the goals of my investigation. To do this, I used BeatifulSoup to scrape player game log data for 139 NBA players across the 2022-2023, 2023-2024, 2024-2025 seasons as well as team roster data for the same seasons. Then I wrote custom functions to aggrgeate and modify the data in order to capture attributes relevant to my driving question. Speaking of which, the central question I'm exploring is:
**What is the relationship between the number of rebounds for a specific player in a given game and other statstics/player attributes?** While indeed the question is broad, it is designed to help build upon the later corresponding prediction problem. The dataset contains 17,954 rows and 14 columns. Each row corresponds to a game log for a particular player. A description of each column follows:
- Player_Name: name of NBA player
- Date: date of NBA game
- TRB: total rebounds for the palyer in the game
- BlK: total blocks for the palyer in the game
- PTS: total points for the palyer in the game
- Team: the team of the player
- Opp: the team the player plays against
- Cur_RPG: the player's current season average rebounds per game before the game
- STD_RPG: the player's current season standard deviation of rebounds per game before the game
- Cur_BPG: the player's current season average blocks per game before the game
- Opp_Avg_Height: the avarge height of the opposing team
- Player_Height: height of player
- Games_Played: how many games the player has played this season before the game
- Prev_Szn_RPG: the player's average rebounds per game in the previous season

From a coaching and developmnet perspective, rebounds are a key indicator of player performance and involvement in the game so being able to understand what influences rebound numbers can help with player evaluation and curating custom workouts to improve perfomance. On the other hand from a sports gambling perspective, insights into what effects rebound numbers can help inform better betting decisions, in particular with player props. No matter what camp you fall in, building a bridge between data-analysis and real-world basketball knowledge has practical implications for fans, analysts, and strategists alike. 