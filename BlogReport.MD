# Streaming Service Analysis and Modeling with Spark

 ![P1](/images/intro.jpg)

Image source: https://www.businessinsider.com/best-music-streaming-service-subscription

## Project Overview
The objective of the project is to learn and demonstrate how to manipulate, analyze and build a predictive model on a large datasets using Spark libraries. The data represents users information, such as name and gender, page views, and additional usage statistics for online music streaming service. The goal is to predict an event that represents churn, i.e. when a user unsubscribes from the service. To achieve this goal, the data needs to be analyzed,  manipulated, and aggregated in a way suitable for modeling. Since the original data contains only few features, they can be used to derive additional statistics and features that can enrich information fed into the model.

## Problem Statement
Clickstream data is an important source of information to analyze and predict user behavior for various websites and online services. The challenge of this data is the size and structure. Due to the large amount of data, frameworks such as Spark is often used to allow to work with data on a distributed platform. Structure of the data is also need to be considered as this is a form of time series data. When working with this data, preserving the order of the events and choosing the right observation and prediction window is important.

## Approach
Given the above constraints, I selected Pyspark libraries for data analysis. I also simplified the problem on selecting prediction window by assuming we will predict off-line on all the data available. In order to predict the outcome for each user, I aggregated clickstream data on a user level. Since each user may have many sessions, I considered this to be important to aggregate on a session level as well to count distinct visits and session-level statistics. Further, sessions could be aggregated for each users. This way we will distinguish between users who regularly interacts with the service vs. users who had few extensive interactions. This approach allowed me to generate variety of features.

## Evaluation Metrics
Since we predict a binary event, i.e. churn or non-churn, this should be modeled as classification problem. We can use binary classification metrics to evaluate and choose the best model. Since the dataset for this problem is imbalanced (there are less churners vs. non-churners), I used Area under the curve as the evaluation metric instead of accuracy. This metric measures how the model predicts relative to the random prediction and is not sensitive to the ratio of target vs. non-target event. Additional metrics such as recall and sensitivity can be used if needed to optimize the model for Type I vs. Type II errors.

## Data preprocessing
As a first step in data preprocessing, I evaluated the dataset for missing values. It is important to make sure we have user and session identifiers to use the data. The small data sample did not have any missing identifiers, while the medium had only few. I dropped the rows with missing identifiers. If other information is missing, such as page name or usage statistics, it can be evaluated for the reasons or imputed if needed. For instance, song name is not populated for all pages, such as ‘Cancellation’. 

## Analysis
The initial analysis was performed on the clickstream data to understand feature types and distributions. Most interesting features were page types. We can use them to identify churn and also to understand and measure type of interactions with the service. 


               page| count|
+--------------------+------+
|            NextSong|228108|
|                Home| 14457|
|           Thumbs Up| 12551|
|     Add to Playlist|  6526|
|          Add Friend|  4277|
|         Roll Advert|  3933|
|               Login|  3241|
|              Logout|  3226|
|         Thumbs Down|  2546|
|           Downgrade|  2055|
|                Help|  1726|
|            Settings|  1514|
|               About|   924|
|             Upgrade|   499|
|       Save Settings|   310|
|               Error|   258|
|      Submit Upgrade|   159|
|    Submit Downgrade|    63|
|              Cancel|    52|
|Cancellation Conf...|    52|
|            Register|    18|
| Submit Registration|     5


I used ‘Cancellation Confirmation’ to encode the churn event. Then I aggregated information on a user level to indicate users who had a churn even as churners. The resulting set contained 99 churners and 349 non-churners. I also added features like tenure by calculating a time interval between first and last interaction, total number of distinct session ids per user as visits, and if a user was paying for the service (at least one paid event exists). I explored the correlation between these features and the churn by using feature distribution and statistics. 

The chart below shows that churners are less likely to pay for the service comparing to non-churners. (69% vs. 75%) 
churn  paid
0      1       0.745665
       0       0.254335
1      1       0.692308
       0       0.307692

![P2](/images/eda1.png)
Users who ended up churning had 25 months as average tenure vs. users who did not churn had 46 months of tenure.

![P3](/images/eda2.png)
Visit frequency is an engineered feature that shows how many sessions per day on average each user had. It reflects the usage of the portal. Users who ended up churning were using the site less frequently (2.75 vs. 5.11 for non-churners).
As the next step, I aggregated data on a session level to calculate the session-level statistics. At this point, I used the EDA to explore the univariate correlation between session-level features and churn.
![P4](/images/eda3.png)

![P5](/images/eda4.png)
Churners played less number of songs per session and had shorter average session duration 
To reflect the sentiment of the user towards the service, I added features such as number of ‘thumbs up’, adding friends, adding songs to playlist, downgrading etc. 
Charts below show that churners were less likely to give ‘thumbs up’ or add friends. These features should be predictive for the model.

![P6](/images/eda5.png)

## Modeling
Since the final dataset had less than 500 rows and only couple dosen features, it would be preferable to use Python scikit learn libraries. However, to satisfy the project requirements and to make this approach suitable for much larger datasets, I continued with Pyspark libraries. 
I split the data into training and testing (70/30). It is recommended to set aside a holdout dataset to test after the model tuning. However, there was not enough data for validation. I used the cross validation approach instead. I also used imputation on numerical features and vector assembler to prepare the dataset for modeling. I utilized Pyspark pipeline to assemble all steps necessary to execute model fitting and scoring.
I started with Logistic Regression and mostly default settings to establish a baseline model. The first model showed fairly good performance of 0.87 and 0.84 AUC on train and test data respectively. I also decided to compare it with more advanced algorithm like boosted decision trees. This is very popular algorithm to solve classification problem. The resulting performance was 1.0 and 0.81 on train vs. test. It was obviously overfitting on the train data. While there is a way to reduce the overfitting, I did not feel that it promises much improvement over baseline model. I decided that for this data Logistic Regression is suitable approach given its relative simplicity and better performance. 
To improve the model further, I added class weight parameter since the ratio of target vs. non-target is about 1:4. The resulting performance showed improvement especially on test data (0.88 vs. 0.87). 
I also attempted to do additional hyperparameter tuning by changing values for elastic net parameter, regularization parameter, maximum iteration and aggregation depth. The process ran for a couple of hours, but the resulting performance was almost the same as the original. After I examined the parameters selected by the best model, aggregation depth was the only one that changed. 
In order by understand how the model were making decisions, I examined feature importance by looking at the model’s coefficients. Paid subscription, number of upgrades and average session duration are positively correlated with churn while visit frequency and tenure have negative correlation with churn

## Conclusion
I primarily used Spark libraries for data cleaning, analysis, modeling and evaluation. I ended up using medium data set. While initial data had over 600K rows, after aggregating on the user level, the resulting data had 99 churners and 349 non-churners. At this point, it would be easier to use Python scikit learn libraries. However, I decided to proceed with the Spark machine learning libraries to satisfy the project requirements and to prepare a scalable approach.  

The Logistic regression classifier was the most effective for this problem. Additional tuning showed slight improvement in test data. The resulting model performance showed 0.8785 AUC on the train and 0.8670 on the test data. This is reasonably good performance given very small training set. Train and test performance metrics are also close that indicate that the model may generalize well on a new dataset. Ideally, we should test on the hold out sample after we tune the model. I skipped this steps given small data set and relied on cross validation approach instead.

If we need to tune the model further and improve the prediction accuracy, I suggest to generate features based on the order of interactions to pick up signals indicating when a user loses interest in the services. This will allow us to timely predict the churn event.


