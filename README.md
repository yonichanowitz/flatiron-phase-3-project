#### phase-3-project

# Overview
using data from 2012-2018, we aim to predict if a Rainbow Six Siege match will result in a win, based on the decisions that a player makes at the start of a match. the decisions will be fed to classification models, which will then be checked for accuracy, precision and a slight bias towards type II (false negative) errors
 
# Business Problem
- **Background**:
Rainbow Six Siege (R6S) is a highly competitive first person shooter game developed by Ubisoft. The game pits two teams of five players each against each other in alternating rounds where the team that completes the objective, or eliminates the opposing team, wins.
The game has become very competitive in its lifespan and has professional competitions, where hundreds of thousands of dollars are offered as prize money has become commonplace.
The game has a ranking system to ensure players are matched fairly against each other. The higher ranking players, are also considered for professional competition
 
- **Problem**:
For newer players, getting more wins is essential to growing in rank, which can lead to better experience, and actual real life money.
 
Getting wins in R6S usually requires three main skills, communication, aim, and choosing the right operator/map/weapon combination for the match.
 
While I have no access to data to asses the first two qualities, the third can be assessed and predicted with data science and the available data
 
What choices in a R6S match lead to a win?
 
- **Goal**:
Using data, predict if a match will be a win based on features that a player chose
 
# Data Understanding
**Why this data?**
This data has tens of thousands of matches from 2012 to 2017, and the details of who won, and what they chose to use, including:
- has data on choices of
    - operator
    - map
    - objective room
    - primary weapon
    - secondary weapon
- length of match
- outcome
- reason of end of match
- player level
 
These features can give an insight into what choices a player made that led to a win, and if there are any patterns
 
**Awareness of old data**
The dataset used in this project was from season five in 2018. four years have passed and several updates to the game have hence taken place, rendering the data itself somewhat moot
however, I am fairly confident that the methods used in modeling and classifying would still predict features that lead to winning a match
 
# Data analysis
 
The **full** actual data was 20 files, with about 900,000+ rows in each
 
I took two of these files, and made smaller csv files of 100,000 rows each to speed up modeling.
 
# Data Preparation
 
I filtered the data by removing any row that didn't have the `game_mode` as `BOMB` being that bomb mode is what's used in professional competition, and the focus of our study.
 
What remained was 26,257 rows.
 
Of the filtered data, there were few numerical columns. `matchid`, `dateid`, `roundduration`, `clearancelevel` and `skillrank`. all of these columns are ***outcomes*** of wins, and expected to be heavily correlated but falsly so. therefore they were dropped before
 
`team` was a variable that I could not figure out what it was representing. to avoid using data that i had no knowledge about, i dropped it as well
 
The data had a column `haswon` that I used as the target variable. all other variables were either arbitrary (`match_id` and similar), directly correlated, due to being outcomes of the target (`rank` and similar) or were categorical. I removed anything non categorical.
 
## Training and Test data sets
To check that my models were performing well, i split the data into `train` and `test` sets.
 
Then using `OneHotEncodeing` i hot encoded the training and test sets, being that all the variables were categorical.
The hot encoded data was used for fitting each of the models, with care taken to only transform the test set, to prevent data leakage
 
**Holdout set**
To ***double check*** the models performance, and utilize the data available to me, i made a second test data set using another of the CSV files in the data.
 
This required some tuning, as there were a different order to the hot encoded variables in the double checking data set
 
# Making Models
I used 3 models, all focused on classification. `DecisionTreeClassifier()`, `KNearestNeighbors()` and `RandomForestClassifier()`. being that the target was boolean and all the features are categorical
 
for every model I
1) made a baseline model
2) used `GridSearchCV` to get the best hyper parameters
3) made a final model with the optimized parameters
 
# Evaluation
Each model was judged based on its F1 score, and having more type II errors, than type I. for both training and test sets to see that it was training in the desired direction. This was accomplished by using `ScikitLearn`'s built in function `classification_report`, and a confusion matrix mapped to a seaborn heat map, producing readouts like so:
 ```
            classification report
              precision    recall  f1-score   support
 
           0       0.68      1.00      0.81      6447
           1       1.00      0.55      0.71      6682
 
    accuracy                           0.77     13129
   macro avg       0.84      0.77      0.76     13129
weighted avg       0.84      0.77      0.76     13129
 ```
<img src='/images/classificatio_report_1.png'>
 
**F1 score** was looked at to make sure the model was accurate with a balance of precision and recall.
**type II errors** were considered, because the stakeholder would prefer winning when a loss is predicted, than lose when a win predicted
 
in addition to a train and test set, being that the data I was using was ***so incredibly large*** I used data from an additional csv file, to validate my validation. I decided to only do this check on the first base model, to ensure there was no major overfitting, and on the last final model, to make sure it performed on a different, larger set of data than the test data
 
**the additional data set had one differently named hot encoded column**
I filled this column with zeros, so as not to give more weight to a variable that the model was not trained on
 
# Conclusion
The best performing model was a `RandomForest()` model with hyper parameters obtained from a grid search.
specifically:
- max_depth=6: i didn't want the tree to overfit, so i settled on 6
- min_samples_split=2: the default number
- random_state=123: trying to diversify the trees a good amount
 
the results from the final `RandomForest()` model, had an acceptable F1 score, and more type II errors than type I. This gave me confidence in its ability to predict a win
 
The resulting features that had the largest effect on resulting a win were
 
<img src='/images/final_model_barchart.png/'>
 
# Recommendations
a new player wishing to improve their win rate, should be using a submachine gun, picking Doc or Caveira, getting 2 kills (not necessarily more) and being on defense. while getting more kills isn't always feasible, through using secondary gadgets that explode, they will have a higher chance of multiple kills, and better win chance
 
another side point that i looked at just because i was curious, for attackers, the recommendation is as well to take explosive secondary gadgets, such as grenades
<img src='/images/attackers_plot_2.png'>
 
Feel free to comment and correct this notebook, all criticism is welcome
 
# Next Steps
Ideally I would like to test this model on my own game data.
If Ubisoft would release an updated version of this data, with all the new operators, with the update that supposedly evens out the heavy lean towards defenders winning, I would try the models on that data, and probably see different recommendations.
Ideally I would also like to compare aiming data and cross reference it with the outcomes of this study. unfortunately, that data is almost impossible to access at the moment
 
# Contact
- Email: yonichanowitz@gmail.com
- Most Social Media sites: @yonichanowitz
 
# Repository Structure
 
```
│   .gitattributes
│   .gitignore
│   index.ipynb
│   README.md
│
├───images
│       attackers_plot.png
│       attackers_plot_2.png
│       classificatio_report_1.png
│       final_model_barchart.png
│       final_tree_scores.png
│
├───pickle_files
│       clf_grid.pkl
│       forest_final.pkl
│       knn_base.pkl
│
└───uploadable_data
        data_last_slice.csv
        data_slice.csv
```