#### phase-3-project


Overview -- Discussed the business problem
Business and Data Understanding
Explain your stakeholder audience and dataset choice here
Modeling -- what models were used and the performance on the train sets
Evaluation - - how did you find out which models were better (In your case, you chose the f1 score. Answer why did you choose that and the scores for them)
Conclusion - Which model was the best model and why
Recommendations --  what would you recommend
Next steps-- What can you do next?
Contact info
Repository structure

# Overview
using data from 2012-2018, we aim to predict if a Rainbow Six Seige match will result in a win, based on the decisions that a player makes at the start of a match. the decisions will be fed to classification models, which will thwn be checked for accuracy, precision and a slight bias towards type II (false negative) errors

# Business Problem
- **Background**:
Rainbow Six Seige (R6S) is a highly competitive first person shooter game developed by Ubisoft. the game pits two teams of five players each against each other in alternating rounds where the team that completes the objective, or eliminates the oposing team, wins.
the game has become very competitive in it's lifespan and has professional competitions, where hundreds of thousands of dollars are offered as prize money has become commonplace.
the game has a ranking system to ensure players are matched fairly against each other. The higher ranking players, are also considered for professional competition

- **Problem**:
For newer players, getting more wins is essential to growing in rank, which can lead to better experience, and actual real life money.

getting wins in R6S usually requires three main skills, communication, aim, and chosing the right oporator/map/weapon combination for the match.

while I have no access to data to asses the first two qualities, the third can be assesed and predicted with data science and the available data

what choices in a R6S match lead to a win?

- **Goal**: 
using data, predict if a match will be a win based on features that a player chose

# Data Understanding
**why this data?**
this data has tens of thousands of matches from 2012 to 2017, and the details of who won, and what they chose to use, including:
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

these features can give an insight into what choices a player made that led to a win, and if there are any patterns

**Awareness of old data**
the dataset used in this project was from season five in 2018. four years have passed and several updates to the game have hence taken place, rendering the data itself somewhat moot
however, I am fairly confident that the methods used in modelling and classifying would still predict features that lead to winning a match

# Data analysis

the **full** actual data was 20 files, with about 900,000+ rows in each

I took two of these files, and made smaller csv files of 100,000 rows each to speed up modelling.

# Data Preperation

I filtered the data by removing any row that didn't have the `game_mode` as `BOMB` being that bomb mode is what's used in professional competition, and the focus of our study.

what remained was 26,257 rows.

of the filtered data, there were few numerical columns. `matchid`, `dateid`, `roundduration`, `clearancelevel` and `skillrank`. all of these columns are ***outcomes*** of wins, and expected to be heavily correlated but falsly so. therefore they were dropped before

`team` was a variable that i could not figure out what it was representing. to avoid using data that i had no knowledge about, i dropped it as well

the data had a column `haswon` that I used as the target variable. all other variables were either arbitrary (`match_id` and similar), direclty correleated, due to being outcomes of the target (`rank` an similar) or were categorical. I removed anything non categorical.

## Training and Test data sets
to check that my models were performng well, i split the data into `train` and `test` sets.

then using `OneHotEncodeing` i hot encoded the training and test sets, being that all the variables were categorical.
The hot encoded data was used for fitting each of the models, with care taken to only transform the test set, to prevent data leakage

**Holdout set** 
to ***double check*** the models performance, and utilize the data available to me, i made a second test data set using another of the CSV files in the data.

this required some tuning, as there were a different order to the hot encoded variables in the double checking data set

# Making Models
I used 3 models, all focused on classification. `DecisionTreeClassifier()`, `KNearestNeighbors()` and `RandomForestClassifier()`. being that the target was boolean and all the features are categorical

for every model I
1) made a baseline model
2) used `GridSearchCV` to get the best hyper parameters
3) made a final model with the optimized parameters

# Evaluation
each model was judged based on it's F1 score, and having more type II errors, than type I. for both training and test sets to see that it was training in the desired direction. This was accomplished by using `ScikitLearn`'s built in fucntion `classification_report`, and a confusion matrix mapped to a seaborn heat map, producing readouts like so:

            classification report
              precision    recall  f1-score   support

           0       0.68      1.00      0.81      6447
           1       1.00      0.55      0.71      6682

    accuracy                           0.77     13129
   macro avg       0.84      0.77      0.76     13129
weighted avg       0.84      0.77      0.76     13129

<img src='/images/classificatio_report_1.png'>

**F1 score** was looked at to make sure the model was accurate with a balance of precision and recall.
**type II errors** were considered, because the stakeholder would prefer winning when a loss is predicted, than lose when a win predicted

in addition to a train and test set, being that the data I was using was ***so incredibly large*** I used data from an additional csv file, to validate my validation. I decided to only do this check on the first base model, to ensure there was no major overfitting, and on the last final model, to make sure it performed on a different, alrger set of data than the test data

**the additional data set had one differently names hot encoded column**
I filled this column with zeros, so as not to give more weight to a variable that the model was not trained on

# Conclusion
the best performing model was a `RandomForest()` model with hyper parameters obtained from a grid search.
specifically: 
- max_depth=6: i didn't want the tree to overfit, so i settled on 6
- min_samples_split=2: the default number 
- random_state=123: trying to diversify the trees a good amount

the results from the final `RandomForest` model, had an acceptable F1 score, and more type II errors than type I. This gave me confidence in it's ability to predict a win

The resulting features that had the larges effect on resulting a win were

<img src='/images/final_model_barchart.png/'>

# Reccomendations
a new player wishing to improve their win rate, should be using a sub-machine gun, picking Doc or Caveira, getting 2 kills (not necessarily more) and being on defense. while getting more kills isn't always feasible, through using secondary gadgets that explode, they will have a higher chance of multiple kills, and better win chance

another side point that i looked at just because i was curious, for attackers, the reccomendation is as well to take explosive secondary gadgets, such as grenades
<img src='/images/attackers_plot_2.png'>

feel free to comment and correct this notebook, all criticism is welcome

# Next Steps
Ideally i would like to test this model on my own game data.
if Ubisoft would release an updated version of this data, with all the new operators, with the update that supposedly evens out the heavy lean towards defenders winning, i would try the models on that data, and probably see different reccomendations.
ideally i would also like to compare aiming data and cross reference it with the outcomes of this study. unfortunately, that data is almost impossible to access at the moment

# Contact
- Email: yonichanowitz@gmail.com
- Most Social Media sites: @yonichanowitz

# Repository Structure
1) images/
    images used in this README from the index.ipynb file

2) pickle_files/ 
    several pickle files of models that I created, to help someone re-running the index file to get results faster. the models, if run without the pickle files, can be expected to take 20-30 minutes MINIMUM to process
    <font color='red'> there was an error uploading these files due to them being too large. the notebook should still run</font>

3) uploadable_data/
    this is a folder with two csv files containing a small fraction of the incredibly large data set that I used. the uploadable data is for viewing, and running the notebook quickly. to get the FULL, but incredibly slow processing verion of the 20gb (yes, gigabyte) data, download it here <a>https://www.kaggle.com/datasets/maxcobra/rainbow-six-siege-s5-ranked-dataset</a>

4) gitignore files

5) README.md
    this document

6) index.ipynb
    a jupyter notebook exploring what features of a match, and player, might be classified to lead to a win, using classification models


