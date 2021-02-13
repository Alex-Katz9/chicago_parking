# Predicting the Payment or Non Payment of Chicago Parking Tickets

## Contents

### Notebooks  
1. Main Notebook for Loading Data: loading_data_chiparking  
2. Main Notebook for Modeling: modeling_notebook_chiparking  
3. Other notebooks for exploratory work and initial baseline models  
### PDFs 
1. Presentation slide deck  
### SQL ReadMes  
Submissions for Metis SQL assignments 1-4  

## Data

Data for this project came from [a ProPublica database](https://www.propublica.org/datastore/dataset/chicago-parking-ticket-data) which consists of both written tickets and tickets given by red light cameras. For the purpose of this project, only the written tickets were considered. Additionally, as the CSV consists of over 50 million observations, only the first million were used as a proof of concept, to limit computing power needs. 

## Scope

As mentioned, the data selected for this project is limited to tickets written in Chicago, or by Chicago officers. Moreover, due to using the first portion of the dataset, tickets were all coming from first few years of the 21st century. In this set, the overwhelming majority of tickets were written for passenger vehicles, so I made the decision to scope to only those vehicles. 

While the ticket data categorizes that status as paid, not paid, awaiting hearing, dismissed, notice sent, and bankruptcy, for the purpose of classification, tickets are labeled as either paid or unpaid (anything that is not paid). From all the features available that described the circumstance surrounding the ticket, features were narrowed down to ones available at the time the ticket was written (so not things like the outcome of a court hearing) and non-collinear (not including latitude, longitude, and zip code all at once). The features included, therefore, where latitude, longitude, violation code, fine dollar amount, and an engineered value that counted how many times a particular license plate had appeared in the data set. 

The goal of the classification was to clearly separate paid from unpaid tickets, to help the city to potentially allocate resources or increase revenue. This could be seen in identifying which characteristics (type of ticket, location, fine amount) that results in payment and allocating resources to writing more tickets of those types or in flagging tickets at the time of writing as likely to be unpaid, therefore needing some kind of immediate notice or reminder. The first use case would benefit from maximizing recall and the second from precision, so for the purpose of this project, I focused on simply maximizing the AUC score. That way, a stakeholder has an opportunity to express their preferred direction and the model could be improved and tuned from there on the entire data set. 

## Initial Data Analysis

Of these first million tickets, once the dataset had been cleaned for tickets with known outcomes, locations, and violations, there was wide distribution across the city. 

![chloro_chi_parking](https://user-images.githubusercontent.com/68957343/107665364-fc3e6e00-6c52-11eb-97d0-bec780c4407b.PNG =10x2)

In the above image, darker colored zip codes relate to a greater accumulation of tickets (up to 18000), while the lighter colors tend toward lower values. There seem to be a few zip codes across the city with higher and lower occurences of ticket, but in general the frequency tends to increase as population density increases in the Near North and Near West areas surrounding the main business district of The Loop. 

Exploratory data analysis  on potential features was completed to determine if any features showed separability on the target. Across numerous featuers, there was not a clear separation in payment, as seen in the example below:  

![lat_long_payment](https://user-images.githubusercontent.com/68957343/107664904-7fab8f80-6c52-11eb-87a9-e41f1ddbee7e.PNG)

This plot shows tickets which were paid in blue and those unpaid in red. Across the city of Chicago, the blue and red observations did not have a discernable pattern. This was similarly seen in the value of the fine, the different violations, and others. So, in order to determine methods of separation beyond these, I turned to constructing models to identify and make use of the culmination of features. 

It is also important to note that the tickets are not balanced between paid and unpaid. In fact, there were about twice as many paid tickets as unpaid tickets in my dataset

## Baseline Modeling

I created initial logistic regression models on individual features, before collecting the features for models that would make use of them together. The initial models, as I expected from my inability to see much separation in EDA returned AUC scores of just barely above 0.50. In this context, this told me they were not performing much better at separating the data than a simple coin flip. Combining all the features in one model produced some small improvement in the AUC score, but viewing a confusion matrix on the resulting predictions also showed a strong tendency towards predicting the paid class of the target, signaling a need to deal with the imbalance in the target. 

After using a random oversampling method to rebalance the training data across the target set, cross validation showed that while the simple logistic models saw a small improvement in their AUC scores, their confusion matrices no longer pointed toward an imbalance in predictions. Combining the features together for a logistic model showed the greatest improvement thus far, but the AUC score was still only around .56. 

## Final Modeling

In an attempt to improve AUC, but potentially lose some interpretability, Random Forest and XGBoost models were fit to the unbalanced and balanced training sets and showed better AUC results than the logistics models immediately. These out of the box results suggest that there is some room for improvement down the road by additional work tuning hyperparameters. Both models fit to the balanced data were right around .70 AUC on the validation data, those were the benchmarks going forward. Similarly, both models' confusion matrices were strikingly similar, so, for the time being, not much separated the two. 

![confusion_matrices](https://user-images.githubusercontent.com/68957343/107853945-4bfd7080-6dde-11eb-95b4-ba69fd4fdc28.PNG)  

In comparing their performance on the unbalanced holdout test data, the XGBoost model showed slightly better performance, not just by AUC but on additional metrics of accuracy, precision, and recall. For this reason, at this time, XGBoost would be the model for continued investigation on future deployment. 

![roc_aucs](https://user-images.githubusercontent.com/68957343/107853948-50298e00-6dde-11eb-9e15-dd0d93ec10e1.PNG)

## Next Steps

First steps going forward would be to continuing fine tuning the XGBoost model to improve performance on this smaller data set. After subsequent gridsearches are performed to identify hyperparameters, retraining the model on the larger dataset would be of interest. At that point, having a model that is fit on all Chicago parking tickets in the last twenty years could be put into production in either of the two cases mentioned earlier. Potentially, for either case, it would be worthwhile to return to the smaller data set and perform the tuning based on optimizing for precision or recall, if one of the use cases seems more pertinent.

Additionally, there were a number of interesting features in the data that were not used for this project as they would not be available at the time a ticket was written. For example, observations included data about court hearings, property seizure, and more. All of these could be useful for additional models that might predict potential outcomes of hearings or how and when the city resorts to seizing private citizens' property. A second data set from ProPublica also includes data from tickets written by the automated red light cameras, which could be used to create additional models for similar uses on those observations. 
