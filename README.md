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

# Data Cleaning and Exploratory Data Analysis
Not much extensive data cleaning was needed after I created the custom dataset. The main issue was some columns did contain missing values. In particular, rows assocaited with a player's first game of the season had missing values for the Cur_RPG, STD_RPG, Cur_BPG columns. The reason lies that in order to caclulate means and standard deiations at least one sample is required, however before the first game the players have not played any previous games and therefore it is impossible to calculate the mean and standard deviation of any statistic. One possible idea would be to impute the missing mean values with the players previous season's statistic averages. However I ultimately decided to simply drop the rows containing the missing values. Since the missing values occured only once per player in a specific season, I only lost a small amount of data by dropping rows with NaN values. Additioanlly I converted the Date column from string objects to datetime objects in order to help with later feature engineering.

<iframe src="assets/clean.html" width="700" height="500" frameborder="0">
</iframe>

In order to get a better understadning of the distribution of rebounds per game across the league, I plotted a histogram. The plot suggets rebounds per game are roughly normally distributed and skewed to the right. Many players tended to have a lower amount of rebounds per game, while there were a few outliers who had higher rebound games. Given an arbitrary player wiith no infromation, the plot would suggest its more likely they have a lower amount of rebounds in a game.

<iframe
 src="assets/uniplot.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

 Next I investigated how the total rebounds feature interacts with other columns in the dataset. By basic intuition it's reasonable to assume there would be some correlation between player's height and their amount of rebounds. To verify whether this intuition holds I plotted box plots in which the y-axis contains rebounds per game and the x-axis is the player's height. In general it appears that as player height incereases, the distribution of rebounds shifts higher to contain more larger values. However there are still a noticable amount of outliers within some height groups. Take for example the distribution for players 76 inches tall. In the NBA 76 inches (6 foot, 4 inches) is not that tall, however there are a decent amount of high rebound perfomances from players that height. Therefore while taller players tend to get more rebounds, it seems there are some other hidden factors also responsible for amount of rebounds in a game.

 <iframe
 src="assets/biplot.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>