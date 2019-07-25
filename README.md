
# Webscraping Reddit API and Natural Language Processing
## Classification of posts on r/California_Politics & r/TexasPolitics

This experience started as a daunting project but quickly became fun and interesting experience. I webscraped the Reddit.com API to gather unique posts from two different subreddits. The first was the California Politics subreddit and the second was the Texas Politics subreddit. At first glance these seemed to provide a very nice contrast with each other as the topics discussed in each would have some overlap when the content centered around national politics and some divergence when the content centered around state and local politics. The hope was that they would provide enough features for the Natural Language Processor to correctly classify a posts origin. 

I decided to set up the classification problem to attempt to predict if a post came from the California subreddit. I had sample sizes that were relatively large (~980 vs. ~930) and almost equal. After running multiple models through the GridSearch I concluded that they were all running well giving a 98-99% accuracy score for the training set and a 92-93% accuracy score for the testing set. The told me that my model did not suffer from a high degree of overfitting.

I decided to re-run my models without including important features such as 'california' & 'texas' to see how this changed the results. The accuracy score of the training set dropped to ~92% and the testing set accuracy dropped to ~82%. 

This told me that these features where playing a very strong role in helping to classify a post correctly. I then explored the Beta values of the features to see how they looked. 

I was able to conclude that the model ran very consistently and that the misclassified posts were generally a result of a particular subreddit post explicitly talking about what was happening in the opposing state.

Overall this was a fantastic learning experience and I thoroughly enjoyed the process.

## URLS

Subreddit California Politics: https://www.reddit.com/r/California_Politics/

Subreddit Texas Politics: https://www.reddit.com/r/TexasPolitics/


## CONTENTS
+ Two jupyter notebooks:  
	- The first contains the data gathering information and process  
	- The second contains the data modeling process and visualization.  
+ A .csv file of our scrapped data titled 'reddit.csv'  
+ A powerpoint file titled 'Subreddit_Presentation.pptx'
+ A folder titled 'images' containing:
	- Multiple images generated by our data
	- Images used throughout the presentation file from the web
	

[Data Gathering Notebook](https://github.com/pwalesdi/Webscraping-Reddit-API-and-Natural-Language-Processing/blob/master/NLP_Data_Gathering.ipynb)

[Data Modeling & Visualization Notebook](https://github.com/pwalesdi/Webscraping-Reddit-API-and-Natural-Language-Processing/blob/master/NLP_Data_Modeling.ipynb)


## DATA GATHERING NOTEBOOK Contents:
- [Import Libraries](#Import-our-Libraries)
- [Creating our URLs](#Instantiate-our-URL)
- [Accessing the API](#Access-Reddit-API-and-Scrape-Posts)
- [Keep only uniqie Posts](#Check-to-be-Sure-Posts-are-Unique)
- [Cleaning and Creating Master DataFrame](#Clean-up-the-DataFrame)
- [Exporting our DataFrame for Modeling](#Export-as-CSV-File)

## DATA MODELING AND VISUALIZATION Contents:
- [Data Import](#Data-Import)
- [Baseline Accuracy](#Calculate-the-Baseline-Accuracy)
- [Data Modeling](#Model-Our-Data)
- [Data Exploration](#Data-Exploration)
- [Taking a Deeper Look at Our Data](#-Dig-Into-the-Data)
- [Prediction and Misclassification](#Where-did-a-misclassification-occur?)
- [Feature Betas](#What-Beta-Values-are-Driving-Our-Model)
- [California Wordcloud](#Analysis-of-California-subreddit-words-via-Wordcloud)
- [Texas Wordcloud](#Analysis-of-Texas-subreddit-words-via-Wordcloud)
- [Looking At Misclassification in Depth](#Examine-the-posts-that-were-misclassified)

## Code Example

```# Deciding which words to remove via stop words
stop_words = ['to', 'the', 'in', 'of', 'for', 'and', 'on', 'is', 'it', 
              'with', 'what', 'about', 'are', 'as', 'from', 'at', 'will', 
              'that', 'says', 'by', 'be', 'this', 'can', 'has', 'how', 
              'california', 'texas']
# Setting up our hyperparameters to pass through our pipeline
pipe_params = {
    'vec' : [CountVectorizer(), TfidfVectorizer()],
    'vec__max_features': [1700, 1900, 2500, 3000],
    'vec__min_df': [2, 3],
#     'vec__max_df': [0.4, 0.5],
    'vec__ngram_range': [(1,2), (1,1), (1,3)],
    'model' : [LogisticRegression(), 
               LogisticRegression(penalty='l1', solver='liblinear'), 
               LogisticRegression(penalty='l2', solver='liblinear'), 
               MultinomialNB(alpha=1.1),
#                RandomForestClassifier(n_estimators=1500)
              ],
    'vec__stop_words': [frozenset(stop_words)],
}

# Defining a function to do our model analysis. This function takes in X, y, and any pipe parameters
def model_analysis(X, y, **pipe_params):
    X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y)
    pipe = Pipeline([
            ('vec', CountVectorizer()),
            ('model', LogisticRegression())])

    gs = GridSearchCV(pipe, param_grid=pipe_params, cv=3, verbose=1, n_jobs=3)
    gs.fit(X_train, y_train)

    print(f' Best Parameters: {gs.best_params_}')
    print('')
    print(f' Cross Validation Accuracy Score: {gs.best_score_}')
    print(f' Training Data Accuracy Score: {gs.score(X_train, y_train)}')
    print(f' Testing Data Accuracy Score: {gs.score(X_test, y_test)}')```

## Visuals
<space><space>
		
### Beta Values for features that are predicting Texas Reddit : Logistic Regression
![alt text](https://github.com/pwalesdi/Webscraping-Reddit-API-and-Natural-Language-Processing/blob/master/images/download-8.png "Beta Values")
<space>
	
### Beta Values for features that are predicting California Reddit : Logistic Regression
![alt text](https://github.com/pwalesdi/Webscraping-Reddit-API-and-Natural-Language-Processing/blob/master/images/download-7.png "Beta Values")
<space>
<space>

### Distribution of Predicted Probabilities : Classifying False Positives & False Negatives
<p align="center">
  <img width="750" height="550" src="https://github.com/pwalesdi/Webscraping-Reddit-API-and-Natural-Language-Processing/blob/master/images/prob_dist.png">
</p>



