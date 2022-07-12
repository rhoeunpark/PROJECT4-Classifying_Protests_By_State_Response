 # ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) Project 5

<h1 align="center">Classifying Protests by State Response</h1>

## Purpose

In attempts to express their objection to a government action, protesters occasionally run into physical conflicts with the state officials who attempt to control the mass mobilization. This project seeks to determine whether it would be possible to distinguish between protests that will lead to a negative or non-negative state response. This goal will be achieved by building a predictive classification model based on various characteristics of the protest movement and open source text collected about the event. 

---

## Data

### Original Dataset:

[*"Mass Mobilization Protest Data"*](https://dataverse.harvard.edu/file.xhtml?persistentId=doi:10.7910/DVN/HTTWYL/TJJZNG&version=5.0) from Harvard Dataverse
- The data cover protests from 162 countries between 1990 and March 2020. 
- 15198 rows, 13 columns

### Cleaned Dataset:

Feature selection and feature engineering was done for the purpose of our project as follows. 

#### Data Dictionary

|Feature|Type|Description|
|---|---|---|
|**region**|*object*|Regions of the world broken into 8 different parts according to country codes.| 
|**year**|*integer*|The year the protest was held in.|
|**protesterviolence**|*boolean*|Whether protesters engaged in violence against the state; value 1 represents violence present.| 
|**participants_category**|*object*|Estimated number of participants categorized to one of the 6 groups of participant number ranges.|
|**length**|*integer*|Estimated length of the protest in days, calculated by subtracting start date from end date.| 
|**demand_political_behavior_process**|*boolean*|Boolean value 1 given to protests with participant demand for broad terms of political change (e.g. who rules, who can participate in elections, democratic transitions, etc).| 
|**demand_labor_wage_dispute**|*boolean*|Boolean value 1 given to protests with participant demand against state policy that influence labor conditions or wage rates.| 
|**demand_police_brutality**|*boolean*|Boolean value 1 given to protests with participant demand against the treatment of citizens by the authorities.| 
|**demand_social_restrictions**|*boolean*|Boolean value 1 given to protests with participant demand against the imposition of constraints on interactions among or between groups, the banning of social behavior, or the banning of the rights of a particular group.| 
|**demand_land_farm_issue**|*boolean*|Boolean value 1 given to protests with participant demand for access to or restrictions imposed on the use of land.| 
|**demand_politician_removal**|*boolean*|Boolean value 1 given to protests with participant demand against official corruption or the corruption of a particular individual, that involves demand for removal of an individual or small group within the government.| 
|**demand_price_inc_tax_policy**|*boolean*|Boolean value 1 given to protests with participant demand over subsidies, tax increases or levies, the cost of food, utilities or other such necessities.| 
|**response_category_2**|*boolean*|boolean value 1 given to negative state response to a protest, which includes 'killings', 'shootings', 'beatings', and 'arrests'.| 
|**response_category_3**|*integer*|Integer value 2 assigned to negative state response including 'killings', 'shootings', 'beatings', and 'arrests'. Integer value 1 is assigned to neutral responses of 'crowd dispersal' and 'accomodation'. Integer value 0 is assigned to the 'ignore' response.| 
|**notes**|*object*|Open-source text comments on the facts of the protest events, after the events took place.| 

---

## Cleaning & EDA - Categorical Features

### Cleaning

The original dataset mainly consisted of categorical variables. And although there were numerical variables, they were either dropped due to their lack of usefulness or converted into categorical variables during the cleaning process, resulting in an all categorical variable dataset.

The features focused on during cleaning were:

- **‘protest’**
   - This feature labeled a row either 0 or 1. Based on the data dictionary, a protest is defined as a gathering of 50 or more people to make a demand of the  government. The action must be ‘home grown’ and targeted at the state police or state policy, meaning a protest that targets the policies of another country is not considered. Rows with zeros were dropped.


- **‘startday’, ‘startmonth’, ‘startyear’, ‘endday’, ‘endmonth’ and ‘endyear’**
   - These features were combined to create start date and end date features. These were used to calculate the length of a protest in days. In the end, all features containing date/length information were dropped as the vast majority of protests were recorded as starting and ending the same day.


- **‘participants_category’ and ‘participants’**
   - These features contained similar information. The ‘participants_category’ feature described the range of the number of participants. There were 7 categories: (50-99), (100-999), (1000-1999), (2000-4999), (5000-10000), (>10000). The ‘participants’ feature was open-text. In some cases, it contained more detailed information on the number of participants. In other cases, it was more general. Because the vagueness of the ‘participants’ feature for some rows made it impossible to obtain specific counts for all protests, the ‘participants_category’ feature became the better feature to use. Any missing values for that feature made use of its corresponding ‘participants’ value to categorize it in one of the 7 categories.


- **‘stateresponse1’ - stateresponse7’**

   - There were 7 state response columns, meaning that for each protest, there could be up to 7 state responses recorded. The 7 response types (in order of severeness), were: 
      - killings
      - shootings
      - beatings
      - arrests
      - crowd dispersal
      - accomodation
      - ignore

   - For the sake of creating a 1 target variable, each protest was reduced to its most severe response. However, a look at the count for each response showed a severe class imbalance, and so the 7 response types were grouped into 2 and 3 classes. 

   - When grouped as 2 classes, killings, shootings, beatings and arrests were grouped as the negative responses, and crowd dispersal, accommodation and ignore were grouped as the non-negative responses.  

   - When grouped as 3 classes, killings, shootings, beatings and arrests were grouped as the negative responses, crowd dispersal and accommodation as the neutral responses and ignore stood alone as no response.


- **‘protesterdemand1’ - protesterdemand4’**

   - Similar to the state response columns, there were 4 protester demand columns meaning that for each protest, there could be up to 4 protester demands recorded. The 7 demand types (in no particular order) were:
   - political behavior, process
   - labor wage dispute
   - police brutality
   - social restrictions
   - land farm issue
   - removal of politician
   - price increases, tax policy

   - Unlike the state response columns, the demands were not reduced down to 1 per protest or grouped as they couldn’t be ranked nor were they as related. In order to work with all demands, a column for each demand was created and contained binary values indicating whether or not the demand was made for a given protest.

### EDA

Given that all features were categorical, there was not much beyond count plots to explore the data with. 

#### Target Variables
Because there were 3 grouping options for the state response (2, 3 or 7 classes), all 3 were explored. 

- With 7 individual classes, there was a severe class imbalance. The ‘ignore' response alone made up about half of all responses. If all classes were to be used during modeling, the model would have a very hard time learning from the classes with fewer counts.

- With 2 classes, each now had a decent count that could be learned from, but the classes were still severely imbalanced with the non-negative class almost 3 times as large as the negative class.

- With 3 classes, the classes became a little more balanced, but ignore remains the main cause of the imbalance and even on its own is 2 times as large as the neutral and negative classes.

#### Predictive Variables

**‘country’**

There were too many countries to consider the country feature as one that should be One-Hot-Encoded. Many protests were also concentrated in a few countries per region, leaving the majority of countries to have little to hardly any protests that would make it very hard for the model to learn from.

**‘region’**

Europe had the most protests recorded. Africa and Asia both also had a good number of protests recorded, but the number of protests per region after that quickly started to diminish.

**‘year’**

The number of protests fluctuated from 1990-2010 but in general, increased. After 2010, however, they increased dramatically.

Splitting apart the protests in 2 classes (negative and non-negative) does not offer much insight. The same trends are seen between the 2 classes, though there are more fluctuations with the non-negative class.

Similar to splitting apart the protests in 2 classes, splitting into 3 classes (negative, neutral and no response) does not offer much insight either.

**‘protester_violence’**

About 1/3 of the protests experienced protester violence.

**‘participants_category’**

Based on the participants_category counts, this feature seems like it could be a good predictor. The 100-999 category has the most protests, but the other categories have a decent amount of protests that the model can learn from.

**‘protester_demands’**

Protests involved the political behavior/process demand more than any other demand. All other demands were seen far less frequently. This may be a difficult predictor to learn from based on these counts.

#### Summary

It was difficult to gain meaningful insights and identify strong predictors with all categorical features. This provides a tip that modeling on this data will be difficult, and it's not likely to find a model that will perform well on it.

---

## Cleaning & EDA - NLP

### Cleaning

For the NLP portion of the cleaning we decided to only focus on two features: ‘notes’ and ‘stateresponse1’ - ‘stateresponse7’.

**‘notes’**

This feature describes any information that might have informed a coding decision. Notes also commented on the facts of the protest events, often quoting from main sources.
For the ‘notes’ column the only cleaning that had to be done was removing the 46 null values.

**'stateresponse1’ - ‘stateresponse7’**

This feature consists of the 7 different ways a protest event was coded depending on how the government responded. These consisted of:
- killing
- shooting
- beating
- arrest
- crowd dispersal
- accommodation
- ignore

For NLP we chose 2 classes for this feature; negative and non-negative. The non-negative class was: ‘ignore’, ‘accommodation’, and ‘crowd dispersal’. And the negative class was: ‘killing’, ‘shooting’, ‘beating’, and ‘arrest’.

### EDA

All of the distributions when analyzing title word count and character length count are right skewed. The average character length between both state responses is 88 characters for the negative state response and 109 for the non-negative state response.

#### WordCloud

Many of the words that populated the word cloud are places or individuals that were part of the protest. Canada is a top word and the country only had 53 entries but when we looked further into it we noticed that in most Canadian notes they tend to use the word Canada or canadian multiple times per note. The country with the most notes was the United Kingdom and we can see the influence with some of the top words that populated.

#### CountVectorizer

CountVectorizer for both state responses was able to identify 37936 words. We decided to run a model for both responses together, negative response, and non-negative response. We see 'police' with 11386 words and in the lead by over 300 words. The top 10 most common words with 'negative' state responses, we did see police with an overwhelming amount. This is because all of the negative state interactions are cause by police. The top 10 most common words with 'positive' state interactions removed the word 'police' from the top most common words and replaced it with 'protest'.

#### TfidfVectorizer

TfidfVectorizer identified 38783 words. We decided to run the model for both responses together, negative response, and non-negative response. The top 10 words for both classes mostly identify the group of people who were protesting (students, teachers, women). For the top 10 most common words for the negative class the major difference was that it seemed to identify words that were more like actions that happened at the protest (riot, gas, killed). For the most common words in the non-negative class it had the same top words as both classes together, except for 'general' was in the most common spot and in the both class most common words it did not even make top 10.

---

## Model Insights - Categorical Features (2 & 3 Classes)

As seen in the EDA, it was unclear what the impact of the year feature would be. With that in mind, models with and without the year feature were run to see if it made a difference. The models also switched off between using 2 classes (response_category_2) and 3 classes (response_category_3) as the target.

The modeling results were categorized in 4 ways:
- 2 classes using year data
- 3 classes using year data
- 2 classes without year data
- 3 classes without year data

Due to the class imbalances in both the 2 classes and 3 classes, the following class imbalance techniques were tested for each of the above mentioned categories:
- Oversampling the least frequent class
- Undersampling the most frequent class with Near Miss
- Weighted models

The following metrics were used to assess model performance:
- train accuracy
- test accuracy
- variance (train accuracy - test accuracy)
- test precision
- test F1 score

Of these metrics, test accuracy, variance and test precision were considered the most. Overall accuracy was the primary interest, but considering precision provided insight as to how well the less frequent class was being classified. The F1 score was also helpful to include since imbalanced classes were being dealt with.

### Baseline Model Results

#### 2 Classes (with year) vs 3 Classes (with year)

The weighted models performed best for both 2 classes and 3 classes. The 2 classes with year data weighted models performed better in terms of test accuracy and a lower variance. They achieved accuracy in the mid 70's (with a variance range of 0.009 and 0.048) while the 3 classes with year data were in the low 60's (with a variance range of 0.06 and 0.07). However, the 3 classes with year data weighted models performed better in terms of precision. They achieved precision in the low 60's while the 2 classes with year data were in the mid-high 40's.

#### 2 Classes (no year) vs 3 Classes (no year)

The weighted models performed best for both 2 classes and 3 classes here as well. The 2 classes with no year data weighted models performed better in terms of test accuracy and a lower variance. They achieved accuracy in the mid-high 70's (with a variance range of -0.0002 and 0.06) while the 3 classes with year data were in the low 60's (with a variance of 0.087). However, the 3 classes with no year data weighted models performed better in terms of precision. They achieved precision in the low 60's while the 2 classes with year data were in the mid-high 40's.

#### Overall

Weighted models performed better than the models with oversampled or undersampled data. The best performing weighted models were Logistic Regression, Support Vector Classifier and XGBoost.

Running models for 2 and 3 classes came with some interesting observations. It was assumed that because the classes weren't going to be as imbalanced as with 2 classes, that they would achieve a higher accuracy, but the opposite happened. The accuracy decreased substantially. However, they did do better with classifying the less frequent classes.

Running models with and without year data also showed that the feature didn't impact model performance much, not even in terms of variance, which was the primary reason for testing this feature. After One-Hot-Encoding the data, when the year data was included, the DataFrame contained 53 features. When the year data wasn't included, the DataFrame contained 23 features, so it was thought that the variance might decrease without the year data, but it didn't make much of a difference. The accuracy and precision scores were also very similar with and without the year data.

Due to the lack of clarity on the impact of using year data and using 2 or 3 classes, all four cases will be modeled and hyper-tuned using Logistic Regression, Support Vector Classifier and XGBoost, in the event that hyper-tuning causes the impact to become clearer.


### Model Tuning Results

Even after some considerable tuning, none of the models did much better than the baseline models, and some actually performed worse. Of the models, however, the best-performing was the Support Vector Classifier when using 2 classes. It achieved the highest test accuracy score of 0.77138, which was the primary goal. The use of year data did not make a difference.

As suspected in the EDA process, this dataset containing all categorical features was not ideal for modeling and there was not a model identified that could be pushed to production.

---

## Model Insights - Categorical Features (6 Classes)

Aside from 2 and 3 class classification, we peaked to see if we could obtain a meaningful prediction model for classes other than the 'ignore' response. Although the majority of the dataset had 'ignore' as an outcome, protesters would most likely be more interested in finding out about the other responses that have greater impact on them. Therefore, this sub-section investigated whether we could predict the state response will involve 'shootings', 'killings', 'beatings', 'arrests', 'accomodation', or 'crowd dispersal', given that the state response is not 'ignore'.

Overall, models to predict the specific state responses without the 'ignore' class does not perform well; just looking at the accuracy, only XGBoost performed slightly better than the baseline model that had 38% accuracy. Precision of all models were higher than the baseline, indicating that it performs somewhat better at predicting the positives than the null model. Even so, with the test accuracy lower than 50%, it is hard to say that the models would be helpful in predicting the state response solely based on the categorical features that we fed into the training model.

Out of 6 different classification models, XGBoost had the highest accuracy score in both training and test set (0.54 and 0.42 respectively). The attempt to hypertune the model yielded training and test accuracy scores of 0.51 and 0.41 respectively; other than reducing the variance slightly, the hypertuning process did not increase the predicting power of the model.

Hoping for a better performing model, we also trained on a neural network deep learning model. The final model had 0.43 training accuracy with 0.41 test data accuracy. The variance was much lower with the neural network model, but it did not have a higher predictive power. The predictive power of the neural network model is comparable to the XGBoost which was the model with the highest performance amongst the 6 different machine learning models. 

Whichever model is used, attempting to work with a smaller dataset without 'ignore' response yields very low accuracy. This is likely due to the fact that almost half of the whole dataset is labeled 'ignore'. Such imbalance of data sets us off with a baseline model that already has a pretty high accuracy score. Also, removing the rows with the 'ignore' label leaves us only with about 8000 rows to work with, which is much less than the ideal amount of data required for the deep learning

---

## Model Insights - NLP

For the NLP portion we decided to run 3 different models:
- Logistic Regression
- XGBClassifier
- MultinomialNB
 
The reason we chose these models was because these were the best performing models for the categorical data.
 
For each one of these models we ran:
- OverSampler or Undersampler
- CountVectorizer
- TfidfTransformer

A sampler was required because of how unbalanced the raw data was. The OverSampler performed much better on every model in comparison to the UnderSampler. As for running both CountVectorizer and TfidfTransformer together, this is because CountVectorizer performs the task of tokenizing and counting, while TfidfTransformer normalizes the data.

After some balancing and tuning, the best performing model was the pipeline with Logistic Regression model, undersampling, CountVectorizer & TfidfTransformer. We got a test score of 0.83, train score of 0.90, and an accuracy score of 0.82. 


---

## Conclusion

The project was split into two main branches in the attempt to distinguish state responses to protest events; modeling with categorical predictors, and NLP based prediction based on the open-source notes about the event. Regardless of the number of classes we ended up using, the model performed poorly working entirely with the categorical predictors. 

With this particular dataset, NLP based model using the open source notes about the event as the predictor performs better at predicting the outcome of the event. More specifically, tuned logistic regression is the best predictor of the outcome, as it had the accuracy score comparable to the XGBoost Classifier with considerably shorter training time. 

### Recommendations & Next Steps

Although NLP based logistic regression was the best performing model in this particular project, it is hard to conclude that its predictive power is good enough to implement into the real-world scenario just yet. If we were to continue working with the current form of dataset with exact same features, it would be helpful to focus more on open source text analysis and collecting more detailed notes. In order to advance in a non-NLP based model, we may need to turn to collecting and working with more numerical features to increase the predictive power. 
