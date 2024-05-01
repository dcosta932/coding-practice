# GloBox A/B Test for Landing Page

## Summary

We tested a new landing page showcasing a banner at the top with a food and drink design.

The goal of the new landing page is to spread more awareness to GloBox’s Food and Drink category in order to  increase revenue.   

For a control in this experiment we used our current landing page.  A /B test was performed with the experimental and control landing page.  
Hypothesis for conversion
H0: p1 = p2, H1: p1 != p2
The Null Hypothesis states that there is no difference in the conversion rates between the A and B group.
Hypothesis for average amount spent
H0: μ1 = μ2, H1: μ1 != μ2

The Null Hypothesis states that there is no difference in the average amount spent between the A and B group.
Context
The experiment ran from January 25th to February 6th, reaching a total of 48,943 users.  Our control group had 24,343 users and the treatment group had 24,600.  

## Results for conversion
The data showed a conversion rate of 4.63% for the experimental page vs 3.92% for the control.

The data equaled a pValue of  0.0001, which is smaller than 0.05 and statistically significant. 
I reject the null hypothesis that there is no difference in the user conversion rate between the control and treatment groups.

A 95% confidence interval was run finding 0.0035, 0.0107
Results for average amount spent
The data showed an average amount spent of $3.378 for the experimental page vs $3.391 for the control.
The data equaled a pValue of 0.944, this is larger than our value of 0.05 and statistically insignificant.  
I fail to reject the null hypothesis that there is no difference in the average amount spent by user between the control and treatment groups.
A 95% confidence interval was run finding -0.439, 0.471
![Average Total Spent by Group](image-9.png)
![Conversion Rate by Group](image-10.png)


![Conversion Rate vs Avg Spent by Device](image-11.png)
IOS led android by $2.61 in average spent and a 3.01% conversion rate.
The treatment group did show strong evidence of a higher conversion rate across all devices while the average amount spent was not a significant difference.
  

![Conversion Rate vs Avg Spent by Gender](image-12.png)
Female customers had a strong lead over other genders in all categories.  Female customers had a higher average spent in the control group while female customers showed a non significant conversion lead in the treatment group.


![Conversion Rate vs Average Total Spent by Country](image-13.png)
While the USA led the market it was split evenly amongst both groups.
Other countries did not follow this trend. In countries like Canada, Mexico, and GBR.  The treatment group had strong evidence of a higher conversion rate and/or a higher average amount spent.



## Novelty Effect
Out of the 2,233 purchases during this period 65% occurred with in the first half of the window, before or on January 31st.  After looking at the conversion rate by Join date and the average spent by purchase date I am comfortable in stating that we are not looking at the novelty effect.

From January 25th to January 31st our conversions were 1663 total, 899 B, 764 A
![Conversion Rate by Join Date](image-7.png)
From February 1st to February 6th our conversions were 570 total, 320 B, 250 A
![Average Spent by Purchase Date](image-8.png)
The Conversion Rate and Average spent both have similiar trend lines when comparing the second half of the A/B test.  This argues that we are not experincing the novelty effect.  

Please see the attached SQL coding to see more into the novelty effect of this A/B Test

## Recommendation
I recommend to continue iterating

While I did find a statistically significant increase in conversions in favor of the experimental group, in my opinion it wasn’t large enough to counter the insignificant results from the average amount spent.  

While the cost of creating and maintaining the banner is low, it does take up valuable space on the landing page.  I believe the experiment should be continued over a longer period of time to capture changing sales trends that could influence this data.

# A/B Test results SQL script
```SQL 
SELECT DISTINCT(us.id), us.country, us.gender, gr.device, gr.group, 
--Using coalesce to replace Null with 0, to not through off the spending averages
SUM(COALESCE(act.spent, 0)) AS total_spent, 
--Using case when to create a column to show which users have converted/purchased
CASE
    WHEN act.spent > 0 THEN 1
    ELSE 0
END AS converted
FROM users AS us
--left joins are used as all user data is needed to be kept
LEFT JOIN activity AS act ON act.uid = us.id
LEFT JOIN groups AS gr ON gr.uid = us.id
GROUP BY us.id, gr.device, gr.group, converted 
-- Ordering by id to help organize for a quick visual check that duplicates have been removed.
ORDER BY us.id
```
# Novelty Effect SQL script
```SQL
-- Novelty test first half of testing period
SELECT act.dt, act.spent, gr.group, gr.join_dt,
act.uid
FROM activity AS act
LEFT JOIN groups AS gr ON act.uid = gr.uid
WHERE act.dt <= '2023-01-31' AND gr.group = 'B'
GROUP BY act.dt, act.spent, gr.group, gr.join_dt, act.uid
-- 1663 total, 899 B, 764 A

-- Novelty test second half of testing period
SELECT act.dt, act.spent, gr.group, gr.join_dt,
act.uid
FROM activity AS act
LEFT JOIN groups AS gr ON act.uid = gr.uid
WHERE act.dt > '2023-01-31' AND gr.group = 'B'
GROUP BY act.dt, act.spent, gr.group, gr.join_dt, act.uid
-- 570 total, 320 B, 250 A
```