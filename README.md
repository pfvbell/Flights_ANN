# Exploring ANN interpretability through predicting flight delay

# Modeling
This project mainly aims to explore the uncertainty in feedforward neural networks. First an artificial neural network was fit. This was an overfit artificial neural network, as can be seen below. The aim was not to fit a very accurate model, but rather to explore the predictions made and the certainty with which they were made. 

![](https://github.com/pfvbell/Flights_ANN/blob/main/accuracy%20vs%20ppr.png)

# Interpretability step 1: Exploring feature importance
Then a logistic regression proxy model was trained using the predictions from the original artificial neural network. This allowed interpretation of the major predictors for flight delay and I found that the departure hour of the flight was the most important predictor (using sklearn's 'permutation importance' function on the proxy model.)

![](https://github.com/pfvbell/Flights_ANN/blob/main/feature_importance.png)

Through plotting the probability of delay against departure hour we see that the probability of delay increases as the scheduled departure hour increases. However, it seems to plateau and then decrease as the scheduled departure hour reaches later into the day. This makes sense because we would expect to see delays accruing during the day. However, it should be noted that the probability of delay changes a relatively small amount overall.

![](https://github.com/pfvbell/Flights_ANN/blob/main/Delay%20Probability.png)

I then plotted the predicted probabilities of delay against the main predictors identified in the permutation importance.

![](https://github.com/pfvbell/Flights_ANN/blob/main/dist_departure_hour.png)

It seems that departure hour is more important than flight count. Overall there seem to be lower probabilities of delay at lower levels of flight count. However, high flight counts are less associated with high delay probabilities later in the day. Indeed, high flight count seems to begin after 5am, which is not surprising. There is a correlation between Scheduled departure hour and scheduled arrival hour, and when both scheduled departure hour and scheduled arrival hour are high the probability of delay seems also to be high. When scheduled departure time and scheduled arrival time are around midnight to 1am there seems to be a higher probability of delay. Indeed, the probability of delay seems to be less closely associated with distance. However, there does seem to be some interaction between distance and scheduled departure hour. There seems to be a trend of long-haul flights departing earlier in the day being more likely to see delays as compared to later in the day.

# Interpretability step 2: Exploring and visualising uncertainty
Eight predictors were then bootstrapped to viusalise the variation accross bootstraps.

![](https://github.com/pfvbell/Flights_ANN/blob/main/variation.png)

It seems that there is a large probability distribution for both classes. This suggests that we cannot be too certain about our predictions for either class. Indeed, the confidence intervals are wide, which suggests that we cannot be confident that the predictions for either class are in a narrow range. Only two of the probability distributions have confidence intervals that do not reach over the threshold (0.5) into the opposite class to the true class.

# Interpretability step 3: Building an abstain bagging model
To further measure the uncertainty of the model we build a model which abstains from making predictions when it is uncertain. The Posterior Probability Ratio (the proportion of predictions which cross the 0.5 boundary).

![](https://github.com/pfvbell/Flights_ANN/blob/main/accuracy%20vs%20ppr.png)

The test accuracy increases as the PPR decreases, apart from an outlier result at PPR=0.2. However, it seems that there is only a very close association between test accuracy and PPR at very low PPR values. This suggests that the PPR threshold for this abstain model may need to be much lower than 0.5 if it is to be highley predictive. Depending on the level of test accuracy needed, it may have to be as low as 0.05. However, this may mean that very few observations could be used. Overall it is clear that many of the bagged predictions are not predicted with high confidence. Indeed, as the PPR decreases the proportion of test observations not abstained also increases. This relationship is close to linear.

# Data Dictionary
The model was based on features that can be measured as the flight takes off.

I estimated the predictive intervals of the model using bootstrapping. We will utilize those predictive intervals to build a new kind of model: a model that refrains from making a prediction when it is not confident.

The included variables are:
ARRIVAL_DELAY: the difference between scheduled arrival and actual arrival, in minutes (positive is late, negative is early).
DISTANCE: the distance between arrival and departure airports, in miles.
SCHEDULED_TIME: the flight's scheduled travel time.
MONTH: the month the flight took off, 1 = January, 2 = February, etc.
SCHED_DEP_HOUR: the scheduled departure time (the hour of the day).
SCHED_ARR_HOUR: the scheduled arrival time (the hour of the day).
FLIGHT_COUNT: the number of flights flying out of the origin airport before noon on a typical day.
DAY_OF_WEEK: the day of the week, 1 = Monday, 2 = Tuesday, etc.
ORIGIN_AIRPORT: the airport the flight took off from.
DESTINATION_AIRPORT: the airport the flight was scheduled to land at.
For the airport codes, see: https://www.bts.gov/topics/airlines-and-airports/world-airport-codes

