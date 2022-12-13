#### phase-3-project

# Problem
## How can a R6S player get more wins?
- **Background**:
Rainbow Six Seige (R6S) is a highly competitive first person shooter game developed by Ubisoft. the game pits two teams of five players each against each other in alternating rounds where the team that completes the objective, or eliminates the oposing team, wins.
the game has become very competitive in it's lifespan and has professional competitions, where hundreds of thousands of dollars are offered as prize money has become commonplace.
the game has a ranking system to ensure players are matched fairly against each other. The higher ranking players, are also considered for professional competition

For newer players, getting more wins is essential to growing in rank, which can lead to better experience, and actual real life money.

getting wins in R6S usually requires three main skills, communication, aim, and chosing the right oporator/map/weapon combination for the match.

while I have no access to data to asses the first two qualities, the third can be assesed and predicted with data science and the available data

- **Question**:
what choices in a R6S match lead to a win?

- **Goal**: 
using data, predict if a match will be a win based on features that a player chose

# Why this data?
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

# In this repository
1) uploadable_data/
    this is a folder with two csv files containing a small fraction of the incredibly large data set that I used. the uploadable data is for viewing, and running the notebook quickly. to get the FULL, but incredibly slow processing verion of the 20gb (yes, gigabyte) data, download it here <a>https://www.kaggle.com/datasets/maxcobra/rainbow-six-siege-s5-ranked-dataset</a>

2) index.ipynb
    a jupyter notebook exploring what features of a match, and player, might be classified to lead to a win, using classification models

3) pickle_files/ 
    several pickle files of models that I created, to help someone re-running the index file to get results faster. the models, if run without the pickle files, can be expected to take 20-30 minutes MINIMUM to process

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
I used 3 models. DecisionTreeClassifier, KNearestNeighbors, RandomForestClassifier. being that the target was boolean and all the features are categorical

for every model I
1) made a baseline model
2) used `GridSearchCV` to get the best hyper parameters
3) made a final model with the optimized parameters

between each step I checked each model's F1 score and type I and type II errors, for both training and test sets to see that it was training in the desired direction. This was accomplished by using `ScikitLearn`'s built in fucntion `classification_report`, and a confusion matrix mapped to a seaborn heat map, producing readouts like so:

            classification report
              precision    recall  f1-score   support

           0       0.68      1.00      0.81      6447
           1       1.00      0.55      0.71      6682

    accuracy                           0.77     13129
   macro avg       0.84      0.77      0.76     13129
weighted avg       0.84      0.77      0.76     13129

<img src='/images/classificatio_report_1.png'>

# Testng Models
in addition to a train and test set, being that the data I was using was **so incredibly large** I used data from an additional csv file, to validate my validation

#### the additional data set had one different column
I filled this column with zeros, so as not to give more weight to a variable that was not trained on

# Assesing Models
I used several methods to asses the models, but paid attention to F1 score mostly. I also was looking for a model that precision was given more weight. this involved using a **heatmap of a confusion matrix** to spot type 1 or type 2 errors

# Conclusion
the results from the final `RandomForest` model, had an acceptable F1 score, and more type II errors than type I. This gave me confidence in it's ability to predict a win

The resulting features that had the larges effect on resulting a win were

<img src='/images/final_model_barchart.png/'>

using a sub-machine gun, picking Doc or Caveira, getting 2 kills (not necessarily more) and being on defense. 

feel free to comment and correct this notebook, all criticism is welcome

# Contact
- Email: yonichanowitz@gmail.com
- Most Social Media sites: @yonichanowitz