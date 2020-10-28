# Expected Pass

#### -- Project Status: [Completed]

## Project Intro/Objective
The purpose of this project was to predict the probability of a pass being completed, and from that, assess player skills.

The data includes:

- IDs such as Player, Game and Team IDs
- Positional data, where passes start and end
- Event type qualifiers, dummy variables based on what type of pass existed.

Effectively predicting the probability of passes has useful applications: 
 * It can be used by coaches to assess individual player skill.
 * It enables identification of undervalued and underplayed players
 * It can be used to identify strongest opponents to enable tactical approaches to individual games.


### Methods Used
* Feature Engineering
* Data Cleaning
* Inferential Statistics
* K-Means Clustering
* Machine Learning (Logistic Regression, XGBoost)
* Data Visualization

### Technologies
* Python
* Pandas, Numpy, jupyter
* StatsModels
* mpl.soccer
* Scikit Learn

## Project Description

The project began with analysing the provided data. It included every pass over the course of one season.

Number of Teams: 20
Number of Games: 380
Number of Players: 716
Number of Passes: 358783

Some features were engineered to help prediction ability, including passing distance, as well as variables linked to pitch location, and if the passer was on the winning team.

The data was split into a train and test set, and scaled using a Standard Scalar fit on only the train set to limit data leakage.

#### KMeans 

To identify the optimal number of clusters for KMeans, silhouette score and elbow method was used, however a key feature of this project is interpretability, not model performance. So at the risk of overfitting, I chose a K of 22, to attempt to cluster 2 pass types per player (roughly). The loss in silhouettte score was marginal.

This led to clearer interpretation, however runs the risk of overfitting. To limit this, I trained the clusterer on only the training data, however its not clear how overfit it is (by the nature of unsupervised learning).

#### Baseline

For the classification, a naive method was used as a baseline, which simply predicted that all passes attempted were completed. Its accuracy and precision score was 0.79, with a recall of 1, and log loss of 7.

#### Logistic regression 

Increased accuracy to 0.84, precision to 0.87, and recall reduced to 0.95. Log loss was 0.36, with AUC of 0.86.

Largest positive coefficients were curiously, where the pass started (x) as well as if it was a throw from the keeper.

The largest negative coefficients were where the pass eneded and if it was a cross or a header, or chipped.

Neither fixing the class imbalance or hypertuning the logistic regression improved performance.

#### XGBoost

I was unhappy with the predictive abilities of the Logistic regression, and so tried xgboost. While it provides less interpretability, it generated better results. After hyper-tuning, it increased accuracy to 0.88, precision to 0.9 with recall at 0.95. Log loss was 0.28 and AUC 0.92

#### Player Skills

Player skills were analysed by generating a metric whereby the probability of a pass was predicted, and if the pass was completed the pass was assigned a risk overcome score of 1 - the probability of the pass occuring. If the pass was incomplete, the pass received 0 - the probability it would occur.

This gives us a score for each pass which represents how much risk has been overcome (or how much risk it took to stop a pass). This led to a players average risk overcome score per pass.

I then developed this metric to be used with the players total passes, to take into account some understanding of constistancy. 

Each player got a score, whereby their average risk overcome score was multiplied by their total number of passes. This is how the top players were ranked. Player 263 was found to be the second top passer in this league, behind player 206.

## Key Questions

#### How does pass difficulty change in different areas of the pitch?

 - According to the model, compared to passes originating in the fifth ninetile (middle of the pitch) most other areas of pass origin had a negative impact on the probability of pass completion. The largest impacts were section 1 and 3, (either side of the teams own goal). The only area which had a positive coefficient was 4, however this was not statistically significant at the 95% confidence interval.
 - However, looking at where passes end is more important. Here, the largest negative coefficient in the model is end_x, and while most pass end sections of the pitch have positive coefficients, section 8 (opponents goal area) has a negative one, while section 2 (own goal) has the largest one.
 - This suggests that pass difficulty does indeed change in areas of the pitch, however the disection of the pitch is less helpfull than anticipated. This could be because not granular enough segmentation was use to identify key weak points.

#### How would you communicate insights from the model for player 263 to a club or media professional?

- Various vizualisations are covered in the notebook, which look at how player 263 compares to players who have a similar play style. Throught generating a probability prediction, and a level of risk overcome with each pass as well as their total passes we can look at how he performs against his peers. 

#### Do groups of players have “passing styles”?

 - Through assigning players labels based on their most popular passing cluster, we group players together. We can see that certain groups of players have a tendancy for chipping, others for passes that are generally more forwards or longer etc. This is covered through vizualisations
         
#### Who are the top passers?

- Similar to the way in which we compared player 263 to his peers, we can look at all plaers. Through summing up all the risk players have overcome in passing and multiplying it by how many passes they attempt in a season, I rank top players. I found that player 263 is a top ranked player (rank 2) attempting a large number of passes, with a above average risk overcome score.

## Limitations

- Ranking top passers like this assumes risk is good - but there is concept of value. Data on if passes led to shots, or goals would help generate this.

- Clustering player types based solely on their most popular pass choice - over-simplification of the game. Many players change position throughought the game and actually fulfill multiple roles, especially those in midfield. Instead of comparing just the most popular cluster between players, a more rigorous similarity score should be applied.

## Future Work

- Further develop out the value of passes. Data on shots and goals would be helpful on this.

- Using optimal K to improve model perfomance.

- Tags to identify player positions would allow like for like comparisons between teams and players.

- Like all sports there is much more nuance than simply looking at league level data. What is risk for one player or team is not necessarily risky for another. Players, and teams all play differently, so perhaps building individual classification models at these lower levels would give a more precise value of expected passes. I beigin to explore this with some cluster vizualisations towards the end of the notebook.
