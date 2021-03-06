# Analysis of Strokes Gained and Club Head Speed in Pro Golf
_Author: Hunter Henninger_

## Introduction:

In the impossible game of golf, Strokes Gained has become the most accurate and meaningful way to measure player efficiency and performance since it began being tracked in 2004. Not until June of 2016 did Strokes Gained fully develop into the four components that it is today: Off the Tee, Approach the Green, Around the Green, and Putting. The sum of all four categories gives a players Total Strokes Gained for a given golf tournament. It essentially compares each stroke a player takes to the rest of the Tour by measuring distance remaining to the hole against a baseline statistic. &quot;Strokes Gained recognizes that sinking a 20-foot putt represents a better performance than sinking a three-foot putt, even though they both count as a single stroke on the scorecard&quot; (Mark Broadie, developer of Strokes Gained). Additionally, Club Head Speed, another relatively new statistic, began being recorded in 2007. On Par 4 and Par 5 tee boxes, radar technology is set up for most tournaments to measure the speed at which the club impacts the ball in miles per hour.

Driving distance on the PGA Tour continues to increase as many young, athletic golfers with swing speeds well above the Tour average (roughly 113-114mph) are experiencing major success on the PGA Tour in recent years. As the popular style of golf shifts towards hitting the ball as far as possible, this study seeks to capture statistical evidence of the impact of Club Head Speed on Total Strokes Gained. Because of physical strength or flexibility limitations, golfers often reach a maximum swing speed. For those struggling to increase their Club Head Speed, the results of this study explicitly quantify areas of the sport that will help their game. Through a process of wrangling, cleaning, manipulating, and modeling an extensive dataset of PGA metrics, this analysis clarifies the disadvantages of having a slower Club Head Speed.

## Gathering Data:

The ShotLink System is the platform for multiple professional golf tours for collecting and disseminating scoring and statistical data for every shot by every player in real-time. The data used in this experiment is collected from the ShotLink database for 50 of the best players on the PGA Tour. For each player, it is broken up into the four Strokes Gained categories: Off the Tee, Approach the Green, Around the Green, and Putting. Initially, the goal was to use data for the top 50 players as of the end of 2018. Due to missing data, there is a list of top players that were not included. This includes some big golfer names such as Justin Rose, Ian Poulter, Adam Scott, Francesco Molinari, Sergio Garcia, and more. However, every golfer used is ranked in the top 100 in the world by Official World Golf Ranking.

Once the four categories were merged and each players&#39; data was combined, the resulting dataset contained over 6,000 observations and over 90 different features. Each observation is a professional golf tournament for a single player and each feature is a measured statistic for that tournament. Because of the scope of this study, any observation that was missing a value in any Strokes Gained or Club Head Speed column was removed. Additionally, any observation where the player was cut, disqualified, or withdrew from the tournament was removed to make each observation comparable. The list of features contained a handful of metrics that take into account a golfers specific rank in a certain aspect of golf. These features were dropped from the dataframe as this analysis focuses on only raw golf statistics that a golfer at any skill level can compare themselves to. After the cleaning process, the dimensions of the dataframe resulted in 4,648 observations and 83 features (including Total Strokes Gained).

However, the dataset contained a large number of missing values. This was the biggest roadblock of the entire study. ShotLink measures many aspects of every stroke taken in a golf tournament including the remaining distance to the hole from various distances (approach shots, putts, etc.). So, for instance, if a player never finds themself approaching the hole from between 200 and 225 yards in the rough, ShotLink will leave that statistic empty for that golf tournament. This type of missingness is defined as NMAR (not missing at random). That is, the data is not missing because it was not tracked, it is missing because that scenario never occurred. NMAR null values are difficult to handle so it was approached with three different methods: ignore, hot-deck imputation, and encoded columns/regression imputation.

For the first method, columns and rows with extensive missing data were simply dropped and a smaller dataset (3,616 observations and 60 features) was saved.

The second method involved dropping columns with more than 500 missing values and imputing the remaining missing values with hot-deck imputation. This means that every missing value was replaced with a randomly selected value from the list of observed values for that specific metric for that specific player. This process sustains the original 4,648 observations and is left with 69 features.

The third method incorporates a few strategies. First, columns and rows with extensive missing data are dropped. For any column still containing null values, a new column is created with a 1 if that value is missing and a 0 if it is not. This step adds 22 new columns. These encoded columns will account for missingness in the regression models. The remaining missing values are then filled using regression imputation. That is, a predicted value is calculated with a linear regression that uses the observed values for that observation as predictors. Method 3 results in a dataframe with 4,538 golf tournaments and 91 statistics.

Handling the missing data results in three fairly different dataframes. The effectiveness of each method is compared in the modeling stage of this study. Approaching the missing data from three different angles ensures quality of the results.

## Exploratory Data Analysis:

#### Averages
Becoming familiar with existing patterns in the data is perhaps the most important stage of this study. This section begins by visualizing player averages for Club Head Speed and Strokes Gained. Witnessing the strengths and weaknesses of each player highlights the best players and demonstrates the ability of golfers to experience success in golf in various ways.

![1](./Images/clubheadspeed.png)

From the bar plot above, it is important to get a sense of the range of club head speeds on the PGA Tour. Bubba Watson, Tony Finau, and Brooks Koepka average the fastest swings at about 123mph and have had plenty of success on the Tour. However, incredibly successful players like Jordan Spieth, Brandt Snedeker, and Matt Kuchar recorded low averages within the 108-113mph range. This brings up the question: how are they competing with the big hitters?

![2](./Images/total_sg.png)

Tiger Woods is considered by many as the greatest golfer of all time and seeing his average Total Strokes Gained reiterates that argument. This chart reassures the quality of Strokes Gained as a metric that actually describes golfer performance because you see the most historically successful golfers near the top (Tiger, Phil Mickelson, Rory McIlroy, etc.).

<br><br>
#### Strokes Gained Correlations
To explore correlations between features, the dataset is broken up into each category of Strokes Gained and the metrics that go along with each. Plotting multiple correlation heatmaps provides a quick dive into which metrics matter the most. Among the four Strokes Gained categories, Approach the Green appeared to have the strongest relationship with Total Strokes Gained while Around the Green has the weakest.

![3](./Images/sg_correlations.png)

<br><br>
#### Club Head Speed
Plotting the distributions of Club Head Speed and Total Strokes Gained against each other in a density plot gives some preliminary insight into their relationship. The plot may not be as upward sloping as expected.

![4](./Images/clubheadkde.png)

#### Going for the Green
An important part of professional golf is making smart decisions on where to hit the ball. Going for the Green is an interesting metric as players attempt to find the &quot;sweet spot&quot; of taking just enough risk.

![5](./Images/goingforgreen.png)

Looking closely, this plot shows somewhat of a peak between 60 and 80 percent for going for the green. The "sweet spot" is obviously different depending on the players' abilities and the golf course, but what this plot shows is that players may lose strokes by going for the green too often.

<br><br> 
There are so many complexities in golf from a numerical standpoint and the analysis can extend to great lengths. With the prerequisite knowledge of these patterns, the goal of this study is to determine how these important relationships change for golfers depending on their Club Head Speed.

## Modeling:

The main goal of the modeling process is to capture the explicit relationship between Club Head Speed and Total Strokes Gained. In doing this, certain aspects of golf are highlighted as advantages or disadvantages depending on a players average swing speed. For each model fit in this process, Strokes Gained: Total is the target variable the model is trying to predict and the long list of PGA metrics are used as the independent predictor variables.

The first step is to compare each method for handling missing values by fitting a baseline linear regression model to the data. By comparing cross-validated R-squared scores on the training data with the unseen testing data, method one proves to explain the most variation in Strokes Gained. This implies that filling in the missing values with &quot;fake&quot; data only makes the predictability of golf performance more difficult. Because of this result, the dataframe created by ignoring the missing data is carried into the next step for model tuning.

The model selection process is an important step in producing the best results. After testing multiple types of regression models, the one that performs the best is used in the final analysis of Club Head Speed. For the sake of interpretability, the models used are: simple linear regression, ridge regression, LASSO, elastic net, random forest regressor, and extra-trees regressor. Because of its regularization process through feature selection, the LASSO model explained the greatest amount of variation in Total Strokes Gained by accounting for 60.32% compared to a baseline model using just the mean. By extracting the coefficients of the LASSO model and ranking them by magnitude, this type of model offers insight into which metrics affect golf efficiency the most. As expected, GIR Percentage and Putting Average affect Strokes Gained more than any other feature in the model.

In order to extract the most insight from Club Head Speed, the data is split into two sets based on the average Club Head Speed of the data (115.51mph). This creates a dataframe of golf tournaments where the players&#39; average Club Head Speed was below 115.51mph and another dataframe of tournaments where the players&#39; average Club Head Speed was above 115.51mph. A LASSO regression is then fit to each of the dataframes. To facilitate comparison, coefficients are extracted from both models and then scored on the entire, original dataset.

The slower Club Head Speed model explains a decreased 58.41% of variation in Total Strokes Gained. That is, using the exact same metrics as the baseline model, it is slightly more difficult to explain the performance of golfers with slower swing speeds. Exploring the resulting coefficients of this model highlights the areas of golf where these kinds of golfers excel. For instance, metrics like Going for the Green - Hit Green Percentage and putting from specific distances like six feet and ten feet are of the strongest predictors for this model.

In comparison, the faster Club Head Speed model explains an increased 61.32% of variation in Total Strokes Gained. This boost in R-squared quantifies the notion that a fast Club Head Speed, and the stats that go along with it (driving distance, etc.), is indicative of player efficiency. Whereas, the success of golfers with slower Club Head Speeds is more difficult to explain. The magnitude of coefficients for the &quot;fast&quot; model are much greater overall. This suggests that golfers that swing the club fast are focusing on fewer aspects of the game to account for their Total Strokes Gained like Fairway Proximity, Driving Distance, and Going for the Green - Hit Green Percentage. Putting statistics more negatively affect a golfers&#39; efficiency in this model compared to the &quot;slow&quot; model. In order to compete, golfers with slow swing speeds must make their long putts and convert birdies at a higher rate than the rest of the field.

## Conclusion:

In the game of golf, especially at a competitive level, players are constantly looking for ways to  maximize Strokes Gained. Before going into this study, it was known that increasing Club Head Speed makes the sport easier and the regression analysis proves it. The method used to explore the relationship between Strokes Gained and Club Head Speed essentially breaks golfers into two categories: slower or faster swing speeds. The largest coefficients of the faster model demonstrate that a fast swing speed allows for a high level of strokes gained and GIR and Scrambling percentages. On the other hand, players with slow swing speeds gain all their strokes on and around the green. These types of players make up for the strokes they lose on the way to the green by sinking medium to long range putts. Therefore, to improve Strokes Gained, this study recommends excellence on the green for competitive golfers that swing the club below the threshold of 115.51mph. If a player can beat the field on and around the green, they can bypass the disadvantage of a slower swing speed.

<br><br>
One important thing to note is that each of the golfers included in this study are of the top 100 best in the world. With the influx of powerful swings coming to the PGA in recent years, the ability of certain golfers with slow swing speeds to sustain their level of competition is somewhat unexplainable by the numbers. The results of this study highlight the impossible success that some golfers have against all odds.

<br><br>
Increasing your club head speed as a golfer is difficult as there are physical limitations to improving it. So how do you improve Strokes Gained?
- Avoiding 3-Putts
- Clutch Putting
- Scrambling
- Going for the Green Successfully
- Sand Saves


## Next Steps:
As I continue my research over time I plan to:
- Collect more data to make the model more accurate and coefficients more precise. More specifically, include players not in the Top 100 in the world.
- Create a way for a competitive amateur golfer to compare themselves to PGA players with similar Club Head Speeds and discover ways that they can improve their game.
- With new technologies like TrackMan, there are measurements of golf swings that are not included in the ShotLink database. The ability to obtain that data is unclear at the time but there is an immense amount of research to be done on those statistics. 
