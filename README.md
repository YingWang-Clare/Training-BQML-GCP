# Training-BQML-GCP
Predict Visitor Purchases with a Classification Model with BigQuery ML on Google Cloud Platform

This is a learning project of Coursera Course: Google Cloud Platform Big Data and Machine Learning Fundamentals.

## About BQML
BigQuery Machine Learning is a new feature in BigQuery where data analysts can create, train, evaluate, and predict with machine learning models with minimal coding.

## Process
1. Create a BigQuery dataset to store models
2. Create and select a BQML model type and specify options:

```SQL
CREATE OR REPLACE MODEL `ecommerce.classification_model`
OPTIONS
(
model_type='logistic_reg',
labels = ['will_buy_on_return_visit']
)
AS

#standardSQL
SELECT
  * EXCEPT(fullVisitorId)
FROM

  # features
  (SELECT
    fullVisitorId,
    IFNULL(totals.bounces, 0) AS bounces,
    IFNULL(totals.timeOnSite, 0) AS time_on_site
  FROM
    `data-to-insights.ecommerce.web_analytics`
  WHERE
    totals.newVisits = 1
    AND date BETWEEN '20160801' AND '20170430') # train on first 9 months
  JOIN
  (SELECT
    fullvisitorid,
    IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
  FROM
      `data-to-insights.ecommerce.web_analytics`
  GROUP BY fullvisitorid) # visitors who bought on a return visit (could have bought on first as well 
  USING (fullVisitorId)
;
```

3. Evaluate classification model performance
