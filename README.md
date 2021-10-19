# Predicting Restauant Inspection Outcomes in Austin, TX

![image](https://user-images.githubusercontent.com/9493836/137847263-a7bf4c03-cd19-4e33-a389-73263af74b97.png)

# The Goal

The goal of this project is to build a model that can predict whether restaurants in Austin, TX, are likely to pass or fail their health inspections.  From this analysis, we can hopefully start to understand what drives favorable scores as these relationships would be of interest to restaurants and consumers alike.  In the case of restaurants, a timely prediction could allow for restaurants to course correct if they're deemed to be likely to receive a low score.

# Motivation & Challenges

### Motivation
- Great opportunity to do some anomaly detection
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
- The dataset captures 3 years-worth of inspections history amounting to 5,825 total inspections.  In leveraging Yelp reviews I've allowed a 60-days lookback window in order to stay within the roughly quarterly inspections schedule and prevent data leakage.  I've also made the assumption that categorical/dummy variables (ie. `has_outdoor_seating`, `takes_reservations`) are static over the three year period.

# EDA

One interesting finding from the Exploratory Data Analysis is that both inspection scores and yelp reviews are left-skewed and tend to be relatively.  Additionally, and encouragingly this tendency is robust to high/low ratings and price point.  As a consumer, we would clearly like to see more high scores than low scores across a variety of restaurants.  On the flip side, this hints at a challenging problem ahead since we see no substantial shift in the distribution across these first couple dimensions which one might hypothesize would impact inspection scores.

<img width="217" alt="image" src="https://user-images.githubusercontent.com/9493836/137853246-fc9a148d-e9d2-461d-ac3a-e68937a49c15.png">. <img width="249" alt="image" src="https://user-images.githubusercontent.com/9493836/137854166-9610755a-272b-4471-b043-aeb86c8e9a98.png">
  <img width="298" alt="image" src="https://user-images.githubusercontent.com/9493836/137853137-f8cfc1f3-9e3b-4fab-9a22-b88f6321d7db.png">

As mentioned previously, the data is highly imbalanced with only 0.9% inspections resulting in a failure (this challenge was a big part of the motivation in choosing this problem).  As show below that trend looks relatively stationary over time.  That said, at the restaurant level we do see variation over time.  Given the somewhat geographic nature of the problem, I also looked at how the inspections were distributed across the zip codes in Austin, but didn't see much evidence of geographic relationship with inspection outcomes.

![image](https://user-images.githubusercontent.com/9493836/137855109-95cdb03f-ba4e-4819-831f-7c09c983f0a6.png)

# Sentiment Analysis

...


<!-- toc -->

## Heading

This is an h1 heading

### Sub-heading

This is an h2 heading
