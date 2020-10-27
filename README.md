
# Predicting Starbucks customer spendings
## Optimizing app offers with Starbucks as part of my Udacity Data Science Nano Degree

---

This project is about Starbucks - so here is some information about Starbucks in brief. The Starbucks Corporation is an American multinational chain of coffeehouses and roastery reserves headquartered in Seattle, Washington. It was founded in 1971 and as of 2020 they operated more than 30,000 places in over 70 countries. In 2019 they made a total revenue of US$ 26.50 billion.
Part 1 - Project Definition
Within the following three segments the problem domain, problem project origin and given data set is explained.
1.1/ Project Overview
Starbucks runs a rewards mobile app which is a way for customers to pay in store or skip the line and order ahead. Rewards are built right in, so they'll collect stars and start earning free drinks and food with every purchase.
The given data set for this project was provided by Starbucks via Udacity as part of my Data Science Nano Degree. The data set contains simulated data that mimics customer behavior on the rewards mobile app. Once every few days, Starbucks sends out an offer to users of the mobile app. An offer can be merely an advertisement for a drink or an actual offer such as a discount or a buy one get one free offer (BOGO). Some users might not receive any offer during certain weeks.
This data set is a simplified version of the real Starbucks app because the underlying simulator only has one product whereas Starbucks actually sells dozens of products. The provided simulated data is stated as for the sake of testing the algorithms and not in order to mimic real people. Furthermore we have to understand that different individuals will respond in different ways on the various given offers. Regarding peoples behavior we should also consider that some people may not want to receive any type of offer, in which case we better do not offer them offers at all.
Example of Starbucks rewards mobile app, Source: Starbucks.comEvery offer has a validity period before the offer expires. As an example, a BOGO offer might be valid for only 5 days. We can see in the data set that informational offers have a validity period even though these ads are merely providing information about a product. That means, if an informational offer has 7 days of validity, you can assume the customer is feeling the influence of the offer for 7 days after receiving the advertisement.
We're given transactional data showing user purchases made on the app including the timestamp of purchase and the amount of money spent on a purchase. This transactional data also has a record for each offer that a user receives as well as a record for when a user actually views the offer. There are also records for when a user completes an offer.
The data is contained in three files:
portfolio.json - containing offer ids and meta data about each offer (duration, type, etc.)
profile.json - demographic data for each customer
transcript.json - records for transactions, offers received, offers viewed, and offers completed

Here is the schema and explanation of each variable in the files:
- portfolio.json
- id (string) - offer id
- offer_type (string) - type of offer ie BOGO, discount, informational
- difficulty (int) - minimum required spend to complete an offer
- reward (int) - reward given for completing an offer
- duration (int) - time for offer to be open, in days
- channels (list of strings)

profile.json
- age (int) - age of the customer
- became_member_on (int) - date when customer created an app account
- gender (str) - gender of the customer (note some entries contain 'O' for other rather than M or F)
- id (str) - customer id
- income (float) - customer's income

transcript.json
- event (str) - record description (ie transaction, offer received, offer viewed, etc.)
- person (str) - customer id
- time (int) - time in hours since start of test. The data begins at time t=0
- value - (dict of strings) - either an offer id or transaction amount depending on the record

1.2/ Problem Statement
So the main purpose of this project is to discover how we can predict customer spendings - the amount of money spent, to be precise. Not just on a population level but on an individual personalized level. Finding a good way to understand what makes people spend money based on different individual and common traits, would enhance Starbucks to provide customers just with the right offers and to not blindly send offers. This overall goal leads to the following questions:
How can we best predict how much money a customers will spend? 
What offers seem to work best regarding customer spendings?
How can customers be characterized regarding spending characteristics?

In general a good approach might be to combine transaction, demographic and offer data to determine which demographic groups respond best to which offer type. 
Since the amount of money that's spend is a numerical and continuous variable we are able to calculate a regression with different types of features we can gather from the data. Therefore different Machine Learning (ML) models such as Linear Regression, Decision Tree Regression or Random Forest Regression. Based on data characteristics some ML models might work better than others.
Looking at the data possible features are peoples demographics (profile.json) or offer type characteristics (portfolio.json). Here, especially peoples demographics might be of interest, since we want to understand what individual differences determine customer spendings.
Another important perspective of analysis is to create a customer-offer-matrix that reflects whenever a customers was successfully influenced by an offer. That is, when a customer received, viewed (!) and completed an offer. It has to be kept in mind that someone using the app might make a purchase through the app without having received an offer or seen an offer. Then we can cluster people by different offer types and observe how certain groups of customers respond to different offers. 
In the end we would expect to have an overview of what factors influence peoples buying behavior and also know different groups of customers that respond to specific offer types (or even don't like offers at all).
