# Predicting the Payment or Non Payment of Chicago Parking Tickets

## Data

Data for this project came from [a ProPublica database](https://www.propublica.org/datastore/dataset/chicago-parking-ticket-data) which consists of both written tickets and tickets given by red light cameras. For the purpose of this project, only the written tickets were considered. Additionally, as the CSV consists of over 50 million observations, only the first million were used as a proof of concept, to limit computing power needs. 

## Scope

As mentioned, the data selected for this project is limited to tickets written in Chicago, or by Chicago officers. Moreover, due to using the first portion of the dataset, tickets were all coming from first few years of the 21st century. In this set, the overwhelming majority of tickets were written for passenger vehicles, so I made the decision to scope to only those vehicles. 

While the ticket data categorizes that status as paid, not paid, awaiting hearing, dismissed, notice sent, and bankruptcy, for the purpose of classification, tickets are labeled as either paid or unpaid (anything that is not paid). From all the features available that described the circumstance surrounding the ticket, features were narrowed down to ones available at the time the ticket was written (so not things like the outcome of a court hearing) and non-collinear (not including latitude, longitude, and zip code all at once). The features included, therefore, where latitude, longitude, violation code, fine dollar amount, and an engineered value that counted how many times a particular license plate had appeared in the data set. 

The goal of the classification was to clearly separate paid from unpaid tickets, to help the city to potentially allocate resources or increase revenue. This could be seen in identifying which characteristics (type of ticket, location, fine amount) that results in payment and allocating resources to writing more tickets of those types or in flagging tickets at the time of writing as likely to be unpaid, therefore needing some kind of immediate notice or reminder. The first use case would benefit from maximizing recall and the second from precision, so for the purpose of this project, I focused on simply maximizing the AUC score. That way, a stakeholder has an opportunity to express their preferred direction and the model could be improved and tuned from there on the entire data set. 

## Initial Data Analysis

## Baseline Modeling

## Final Modeling

## Next Steps
