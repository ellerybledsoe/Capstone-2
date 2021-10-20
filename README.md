# Predicting Restauant Inspection Outcomes in Austin, TX

![image](https://user-images.githubusercontent.com/9493836/137847263-a7bf4c03-cd19-4e33-a389-73263af74b97.png)

# The Goal

The goal of this project is to build a model that can predict whether restaurants in Austin, TX, are likely to pass or fail their health inspections.  From this analysis, we can hopefully start to understand what drives favorable scores as these relationships would be of interest to restaurants and consumers alike.  In the case of restaurants, a timely prediction could allow for restaurants to course correct if they're deemed to be likely to receive a low score.

# Motivation & Challenges

### Motivation
- Great opportunity to do some anomaly detection (a common machine learning problem)
- The data is highly class imbalanced

### Challenges
- The data is highly class imbalanced :) 
- Many restaurants have few or no reviews
- Relatively small number of inspections.

# The Data

### Datasources:
- [Austin Restaurant Inspection Scores](https://data.austintexas.gov/Health-and-Community-Services/Food-Establishment-Inspection-Scores/ecmv-9xxi) from the Austin Public Health Department
- [Restaurant Reviews](https://www.kaggle.com/yelp-dataset/yelp-dataset) from Yelp

### High-Level Info:
- The dataset captures 3 years-worth of inspections history amounting to 5,825 total inspections.  In leveraging Yelp reviews I've allowed a 60-day lookback window in order to stay within the roughly quarterly inspections schedule and prevent data leakage.  I've also made the assumption that categorical/dummy variables (ie. `has_outdoor_seating`, `takes_reservations`) are static over the three year period.

# EDA

One interesting finding from the Exploratory Data Analysis is that both inspection scores and yelp reviews are left-skewed and tend to be relatively high.  Additionally, and encouragingly this tendency is robust to high/low ratings and price point.  As a consumer, we would clearly like to see more high scores than low scores across a variety of restaurants.  On the flip side, this hints at a challenging problem ahead since we see no substantial shift in the distribution across these first couple dimensions which one might hypothesize would impact inspection scores.

<img width="217" alt="image" src="https://user-images.githubusercontent.com/9493836/137853246-fc9a148d-e9d2-461d-ac3a-e68937a49c15.png">. <img width="216" alt="image" src="https://user-images.githubusercontent.com/9493836/137926410-8b1a51d8-0f52-4a3f-97d4-bf3d2944db54.png">
  <img width="249" alt="image" src="https://user-images.githubusercontent.com/9493836/137854166-9610755a-272b-4471-b043-aeb86c8e9a98.png">
  <img width="298" alt="image" src="https://user-images.githubusercontent.com/9493836/137853137-f8cfc1f3-9e3b-4fab-9a22-b88f6321d7db.png">

As mentioned previously, the data is highly imbalanced with only 0.9% of inspections resulting in a failure (this challenge was a big part of the motivation in choosing this problem).  As show below that trend looks relatively stationary over time.  That said, at the restaurant level we do see variation over time.  Given the somewhat geographic nature of the problem, I also looked at how the inspections were distributed across the zip codes in Austin, but didn't see much evidence of geographic relationship with inspection outcomes.

![image](https://user-images.githubusercontent.com/9493836/137855109-95cdb03f-ba4e-4819-831f-7c09c983f0a6.png)


# Sentiment Analysis

I explored sentiment analysis as a way to draw out more details on the qualitative nature of the Yelp reviews.  Because the EDA in this area pre-supposes some grouping of positive and negative reviews in order to create, for instance, new features based on ngrams the process becomes a bit self-fulfilling and perhaps introduces multicollinearity.  As an alternative, I wound up pulling `review_length` and `review_word_count` into the model building. 

# Building the Model

There are a couple different approaches I tried here which are explained in more detail below.

### Autoencoder

In building the autoencoder, the high level idea is that you can train the model on our majority class, in this case `passed` inspections, in order for it to become good to predicting that class and subsequently predict our minority class, `failed` inspections.  By building in the model in this fashion the goal would be to find a clear difference in the distribution of reconstruction error between our majority class and our anomalies which is what we're interested in.  I tried a few different hyperparameter settings and ultimately with the following:

- 2 hidden layer decoder using a relu activation function
- 2 hidden layer encoder using a relu activation function and an output layer using a sigmoid activation
- trained for 40 epochs with an early stop on a mean squared error loss functions
- batch size = 20
- the model for 32 each before stopping out

Below you can the distributions of the reconstruction error and naturally the low volumne of `fails`.

![image](https://user-images.githubusercontent.com/9493836/137924526-9af25de4-09f0-445d-b7cf-462a8daa901e.png)

It appears *maaaaybe* the small set of data points has a central tendency to the right of that of our `passes` but it's not clear; additionally, only a couple of observations fall outside the chosen 2 standard deviation threshold.  From this analysis, I can't conclude that there is any meaningful separation between the two classes.


### Isolation Forest

An isolation forest as a second approach seemed like a good option since the model doesn't rely on distant-based calculations, and as result you can train it without scaling the data.  The fnal version of this model has max_samples set to 100 and the number of estimators set to 20.  This model achieved 87.5% recall in predicting failed inspections but had a very low precision of 0.9% and thus is not general-izable unless their is little or no cost for false positives.  In this case, as a restaurant manager that amounts to believing that you're likely fail an inspection.  Perhaps here the cost of failing is so high (ie. temporary restaurant closure, defection of patrons) that it's worth the extra precaution and investment to stave off even a low chance of failure.

<img width="423" alt="image" src="https://user-images.githubusercontent.com/9493836/137949437-2d273a30-3d55-47c4-a7e4-b76b1afcff9d.png">


### Take-aways

Predicting restaurant inspections has proven to be a challenging problem.  As next steps, I would try to gather more data if possible (perhaps state-wide) and invest some time buidling new features.  While the isolation forest way overshoots the mark in terms of false positives, in this scenario I would rate the cost of a false positive much lower that of a true positive which puts a restaurant's business as a whole at risk.  Thus if the false positives can be reigned in while still maintaining good recall this would be a good outcome.  Given the wide range of contexts in which class imbalance arises, I think this is an intriguing area for further exploration.
