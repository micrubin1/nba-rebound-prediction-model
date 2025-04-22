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
Not much extensive data cleaning was needed after I created the custom dataset. Although many of steps I took during the creation of the dataset could be conisdered data cleaning. For example I converted player heights from feet to inches to make comparing them easier and removed all diacritics from player names (particularly Eastern Europen players) to help the process of searching player names in rosters. After, the main issue was some columns did contain missing values. In particular, rows assocaited with a player's first game of the season had missing values for the Cur_RPG, STD_RPG, Cur_BPG columns. The reason lies that in order to caclulate means and standard deiations at least one sample is required, however before the first game the players have not played any previous games and therefore it is impossible to calculate the mean and standard deviation of any statistic. One possible idea would be to impute the missing mean values with the players previous season's statistic averages. However I ultimately decided to simply drop the rows containing the missing values. Since the missing values occured only once per player in a specific season, I only lost a small amount of data by dropping rows with NaN values. Additioanlly I converted the Date column from string objects to datetime objects in order to help with later feature engineering.

<iframe src="assets/clean.html" width="700" height="300" frameborder="0">
</iframe>
In order to get a better understadning of the distribution of rebounds per game across the league, I plotted a histogram. The plot suggets rebounds per game are roughly normally distributed and skewed to the right. Many players tended to have a lower amount of rebounds per game, while there were a few outliers who had higher rebound games. Given an arbitrary player wiith no infromation, the plot would suggest its more likely they have a lower amount of rebounds in a game.

<iframe
 src="assets/uniplot.html"
 width="700"
 height="600"
 frameborder="0"
 ></iframe>

 Next I investigated how the total rebounds feature interacts with other columns in the dataset. By basic intuition it's reasonable to assume there would be some correlation between player's height and their amount of rebounds. To verify whether this intuition holds I plotted box plots in which the y-axis contains rebounds per game and the x-axis is the player's height. In general it appears that as player height incereases, the distribution of rebounds shifts higher to contain more larger values. However there are still a noticable amount of outliers within some height groups. Take for example the distribution for players 76 inches tall. In the NBA 76 inches (6 foot, 4 inches) is not that tall, however there are a decent amount of high rebound perfomances from players that height. Therefore while taller players tend to get more rebounds, it seems there are some other hidden factors also responsible for amount of rebounds in a game.

 <iframe
 src="assets/biplot.html"
 width="700"
 height="600"
 frameborder="0"
 ></iframe>

 To further examine how the total rebounds per game interact with other features, I used groupby functions to display how many rebounds a player has on average against specific teams. For example, in the table below (which is sorted in asecnding order) the first row indicates that a player on average has 4.94 rebounds when playing against the New York Kincks. This may suggest that the Knicks play better box-out defense causing opposing players on avergae to have worse reboudning perfomances. However the variance of the avergae rebounds allowed is quite small and the range is even less than one. Therefore while the opposing team may play some role in a player's rebounds that game, it likely will not have a significant effect due its lack of variation in decribing rebounds per game.

<div style="display: flex; justify-content: start; margin-left: 100px; gap: 20px; flex-wrap: nowrap;">
  <iframe src="assets/groupl.html" width="400" height="800" frameborder="0"></iframe>
  <iframe src="assets/groupr.html" width="400" height="800" frameborder="0"></iframe>
</div>



As mentioned earlier no imputations were perofmaned since all missing values were removed from the dataset. I considered the idea of imputing current average rebounds/blocks per game for the first game of the season with past season averages. However I decided against this because in general there is a lot of variability and uncertianty within the first game(s) of the NBA season. It can sometimes takes a while for players to perform up to their expectations from previous seasons. Therefore since I do not believe removing rows for player's first game of the season takes away siginifcant infromation from the dataset, I decided to drop the missing value rows.

# Framing a Prediction Problem
The prediction problem I aim to solve is: **Predict how many rebounds a player will have in a specific game**. Although in reality the number of rebounds can only be an integer number, regression is still suitable for this task as decimal outputs still have an intuitive meaning in this context. At the time of prediction, relevant features such as current average rebounds/blocks per game will be avaiable because we defined them to be the current average at the time before the game. Other features such as heights and teams are also obviously known at time of prediction as height is always obsevrable and games are scheduled in advance. I chose the response variable to be total rebounds becuase I am interested in studying how machine learning models can assist in player prop betting startegies. Also more broadly speaking I'm interested in the idea of how historical data can help predict future results. To evalaute my model I will be using the mean sqaured error metric. While I could have potentially used mean absolute error, I want to ensure that predictions farther off from their true value are assigned a larger penalty. Under mean absolute error, far-off predictions are not punsihed as severly compared to mean sqaured error due to the squared term, therefore I selected mean sqaured error for my performance metric.

# Baseline Model
For my baseline model, I implemented a Linear Regression model to predict the number of rebounds a player will have in a specific game. The model utilizes two features:

1. Current Average Rebounds Per Game (Cur_RPG)
2. Player Height

Both features are quantitative and continuous in nature. Since there are no ordinal or nominal variables in this version of the model, no encoding was required. After splitting the dataset into training and test sets to evaluate the model's ability to generalize to unseen data, the model achieved the following performance metrics:

- Training Mean Squared Error (MSE): 7.5993
- Test Mean Squared Error (MSE): 7.0729

This performance is considered reasonably good for a baseline model. Notably, the test MSE is approximately half the size of the variance of the target variable on the test set (13.9071). This suggests the model performs significantly better than a constant (mean) prediction baseline, which is an encouraging sign. The model captures key physical and statistical indicators that are intuitively important for predicting rebounds. Player height influences rebounding ability due to reach and positioning, while current average rebounds per game reflects recent form and role within the team. Although simple, the baseline model establishes a solid foundation for further improvement.

# Final Model
To improve upon my baseline model, I added several features that I believe capture meaningful aspects of the data generating process for a player's rebounds in a given game:

1. Team – A player's team can influence rebound opportunities based on style of play. For example, teams with poor outside shooting may create more offensive rebound chances, and teams with strong interior defense may affect defensive rebounds.

2. Opposition Team (Opp) – The opposing team can impact rebounds depending on their shooting efficiency, defensive strategy, and overall pace of play, all of which affect rebound distribution during a game.

3. Standard Deviation of Current Rebounds per Game (STD_RPG) – While average RPG reflects central tendency, the standard deviation captures consistency of performance. More volatile players might be harder to predict, so this feature helps adjust for that uncertainty.

4. Current Average Blocks per Game (Cur_BPG) – Blocking and rebounding both require similar physical traits (e.g., height, timing, verticality). Players who block shots may also be positioned well for rebounds.

5. Opponent Average Height (Opp_Avg_Height) – Taller opponents are likely more competitive on the boards. Including this metric helps account for matchup difficulty.

6. Games Played – This reflects sample size reliability. Early in the season, averages based on a small number of games may not be stable, so including games played helps the model understand variance in performance.

7. Previous Season RPG (Prev_Szn_RPG) – Early-season stats may not be reflective of a player’s true average, so past season performance provides contextual grounding and historical consistency.

8. Day of the Week – There may be subtle, hard-to-detect performance patterns depending on the day (e.g., fatigue, travel schedules), so including this adds temporal context.

To prepare the features, I:

- Applied standard scaling to all numerical features to ensure fair regularization treatment.

- Specifically to obtain the day of week feature I transformed the date column of datetime objects into strings corresponding to the day of the week using the datetime day_name function.

- Used one-hot encoding for categorical variables (team, opponent team, day of the week) to convert them into a usable format without assuming ordinal relationships.

To develop my final model, I experimnetd with three candidate modeling algorithms: linear regrsion, ridge regression, and lasso regression. Ultimately all candidates perfomed very similar to one another - however ridge regression had the best test MSE by a slight margin. Therefore I selected ridge regression for my final model, which is a regularized version of linear regression that penalizes large coefficients to reduce overfitting. For the final model, the only hyperparameter I cosndiered tuning was the regularization strength. To actually select the optimal regularization strength (alpha), I used GridSearchCV with cross-validation. The grid search tested several values of alpha, and ultimately selected alpha = 100. Using the same training and test set used to evaluate the baseline model, the final model achieved the following performance metrics:

- Training Mean Squared Error (MSE): 7.3973
- Test Mean Squared Error (MSE): 6.9673

The final model slightly improves over the baseline in both training and test error, with about a 1.5% decrease in the test MSE. While modest, this improvement shows that the additional features captured new predictive signals beyond what the baseline provided. This was expected, as the baseline already included two strong predictors (current rebounds per game and height), but the added features enabled the model to better contextualize performance and matchups.

Overall, the final model is a meaningful upgrade over the baseline — it generalizes slightly better and is more robust due to regularization and more expressive features, all grounded in a logical understanding of basketball performance dynamics.



