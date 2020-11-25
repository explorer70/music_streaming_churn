# music_streaming_churn
Repository for Udacity Spark capstone project to predict churn for music streaming service

## Table of Contents

1. Motivation
2. File Descriptions
3. Conclusions
4. Licensing and Acknowledgements

### 1. Motivation
The objective of the project is to learn how to manipulate, analyze and build a predictive model on a large datasets using pyspark libraries. The data represents the user information, page views and additional statistics for online music streaming service. The goal is to predict an event that represents churn, i.e. when a user unsubscribes from the service. To achieve this goal, the data needs to be analyzed and manipulated and aggregated in a way suitable for modeling. Since the original data contains only few features, they can be used to derive additional statistics and features that can be used for modeling. 

### 2. File Descriptions

The blog post contains the additional informaiton on the project, summary of the The only additional file is the notebook that contains all the information on data manipulation, analysis, model development, and model evaluation. The original dataset was not apploaded due to its size. It is available through Udacity program.


### 3. Conclusions

Augmenting the original dataset with additional features allowed to build a fairly accurate model. Features like tenure, and frequency of visits were the most predictive. Users with more tenure and higher frequency of interactions were most likely to continue using services. Additionally, the engagement of the user such as adding friends, giving thumbs up to the songs contributed to the positive outcome. If further improvement of the model is needed, we can consider the order of interactions to catch signals such as high original interest in the service followed by disangagement. This approach will also help to determine when the churn will happen.

Most of the features were numeric that allowed to build a model with minimum preprocessing. Binary logistic regression classifier showed a very good performance. Some tuning such as assigning class weights and changing regularization settings helped to improve it further. One note is that this is still a fairly small sample, only 99 churners may behave differently on a real size dataset. Additionally, Spark libraries were not necessary for the modeling part after user-level aggregation. It will be more relevant for real-life scenarios, when modeling on hunderds of thousands of users with the many millions of interactions.

### 4. Licensing and Acknowledgements

Code was created based on the examples fron Udacity Data Scientist nano degree and a course on Spark for the capstone project. 
Stack overflow site was used to find code snippets for pyspark utilities such as unix timestamp conversion and manipulation (https://stackoverflow.com/questions/45977449/convert-timestamp-to-date-in-spark-dataframe_
I also followed some suggestions from the article by Dhiraj Rai on Logistic Regression in Spark ML https://medium.com/@dhiraj.p.rai/logistic-regression-in-spark-ml-8a95b5f5434c

