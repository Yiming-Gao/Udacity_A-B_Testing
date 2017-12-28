# Background
At the time of this experiment, Udacity courses currently have two options on the home page: "start free trial", and "access course materials". If the student clicks "start free trial", they will be asked to enter their credit card information, and then they will be enrolled in a free trial for the paid version of the course. After 14 days, they will automatically be charged unless they cancel first. If the student clicks "access course materials", they will be able to view the videos and take the quizzes for free, but they will not receive coaching support or a verified certificate, and they will not submit their final project for feedback.

In the experiment, Udacity tested a change where if the student clicked "start free trial", they were asked how much time they had available to devote to the course. If the student indicated 5 or more hours per week, they would be taken through the checkout process as usual. If they indicated fewer than 5 hours per week, a message would appear indicating that Udacity courses usually require a greater time commitment for successful completion, and suggesting that the student might like to access the course materials for free. At this point, the student would have the option to continue enrolling in the free trial, or access the course materials for free instead. This screenshot shows what the experiment looks like.

The hypothesis was that **this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time—without significantly reducing the number of students to continue past the free trial and eventually complete the course**. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.

**The unit of diversion is a cookie**, although if the student enrolls in the free trial, they are tracked by user-id from that point forward. The same user-id cannot enroll in the free trial twice. For users that do not enroll, their user-id is not tracked in the experiment, even if they were signed in when they visited the course overview page.

# Experiment Design
## Metric Choice
### Invariant Metrics
Invariant metrics should not change across experimental or control groups.

**Number of cookies**: The number of unique cookies to view the course overview page. This is a *population sizing metric* to be split evenly between the control and the experiment group.

**Number of clicks**: That is, number of unique cookies to click the "Start free trial" button (which happens before the free trial screener is trigger). 与number of cookies类似，不依赖于我们怎么显示"start free trial"页面，因为点击的用户在点击之前是没有看见过那个页面的。

**Click-through probability**: The number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page. 再次，用户在点击button之前是没有看见过我们要测试的页面的，it doesn't depend on our test and is a good invariant metric.

### Evaluation Metrics
Evaluation metrics are expected to change over the experiment with differences observed between the experimental and control groups. **If the hypothesis is true, the number of user-ids to enroll would be reduced, eliminating the unprepared students, with payments/checkouts not decreasing.**

In order to launch the experiment, we want to decrease the enrollment of unprepared students, i.e the gross conversion should decrease, while not reducing the number of students who complete the free trial and make a payment, i.e the net converion should either stay the same or go up.

**Gross conversion**: The number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button. It's related to **"the number of user-ids to enroll would be reduced"**.
- Experiment group: see the pop-up
  - If he has enough time, enroll the course
  - If not, continue with the free trial without enrolling
- Control group: no such pop-up
- Underlying assumption: the gross conversion in the control group is higher than experiment group.

**Retention**: The number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by number of user-ids to complete checkout. It's related to **"without significantly reducing the number of students to continue past the free trial and eventually complete the course"**.

**Net conversion**: The number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button. It is the product of previous two evaluation metrics, and it can be considered as a more **general goal** of the A/B test --- **whether rendering a "5 or more hours per week" suggestion helps increase the ratio of users who make payment over those who see the start free trial page.** Therefore it is also a good evaluation metric.

### Neither
**Number of user-ids**: Since we're interested in the proportion of unique cookies that click on the "start free trial" button, i.e the probability of enrolling, this metric would not give us any useful information and therefore it was not used as an invariant metric.

## Measuring Standard Deviation
要求：For each of your **evaluation metrics**, make an analytic estimate of its standard deviation, given a sample size of 5000 cookies visiting the course overview page. 

|                Baseline Information                 |  Value  |
|:---------------------------------------------------:|:-------:|
| Unique cookies to view course overview page per day:|  40000  |
| Unique cookies to click "Start free trial" per day: |   3200  |
| Enrollments per day:                                |    660  |
| Click-through-probability on "Start free trial":    |    0.08 |
| Probability of enrolling, given click:              | 0.20625 |
| Probability of payment, given enroll:               |  0.53   |
| Probability of payment, given click:                |0.1093125|

For Bernoulli distribution with probability p and population N, the analytical standard deviation is `std = sqrt(p * (1-p)/N)`.

The analytical standard deviation will likely match the empirical standard deviation as **the units of diversion for the experiment and analysis is cookies**. While for the metric retention, given that the units of diversion and analysis are different (user-id vs cookies), it is likely that the analytical standard deivation and the emperical standard deviation will not match.

### Gross conversion
The baseline probability is `p = 0.20625`, and the number of users who see see the "start free trial" page (the denominator of the gross conversion) is `N = 5000 * 0.08 = 400`. Therefore the standard deviation is `sqrt(0.20625 * (1-0.20625)/ 400) = 0.0202`.

分析：The unit of analysis here is a person who click the "start free trial" page, and the unit of diversion is a cookie that does so. They are highly correlated, but not exactly the same (because the same person could visit the same page with a different cookie, say using a different device or a different browser). The high correlation suggests that the analytical estimate is mostly accurate. If we have time, collecting more data to verify it empirically will not hurt.

### Retention
The baseline probability is `p = 0.53`, and the number of users who enrolled the free trial (the denominator of the retention rate) is `N = 5000 * 0.08 * 0.20625 = 82.5`. Therefore the standard deviation is `sqrt(0.53 * (1-0.53)/ 82.5) = 0.0549`.

分析：The unit of analysis here is a a person who enrolled the free trial, and the unit of diversion is the user-id that does so. This two almost always match up (only in very rare cases, the same person might register with several different user-ids, or several people might share the same user-id, although not they are supposed to). Therefore, the analytical estimates should match the empirical one well given the unit of analysis and unit of diversion have very strong match.

### Net Conversion
The baseline probability is `p = 0.1093125`, and the number of users who see the "start free trial" page (the denominator of the net conversion) is `N = 5000 * 0.08 = 400`. Therefore the standard deviation is `sqrt(0.1093125 * (1-0.1093125)/ 400) =0.0156`.

分析： The unit of analysis and unit of diversion are both the same for "gross conversion" metric, and the analysis directly applies here. The analytical estimate is expected to be mostly accurate, but collecting more data to verify if one has time will be even better.

## Sizing (Number of Samples vs. Power)
Using the analytic estimates of variance, how many pageviews total (across both groups) would you need to collect to adequately power the experiment? Use an alpha of 0.05 and a beta of 0.2. Make sure you have enough power for each metric.

回答：

We will not use Bonferroni correction, because metrics here are highly correlated which makes Bonferroni corrections too conservative to draw conclusions.

Use this online calculator to compute the number of samples needed: http://www.evanmiller.org/ab-testing/sample-size.html. 不要忘记乘2.

- **Gross conversion.** The baseline rate is `0.20625`, `d_min = 0.01`. The required number of samples calculated from the online calculator is 25,835. Note that this is the number of clicks on "start free trial", and in order to get that number, we need `25835 / 0.08 * 2 = 645,875` page views.

- **Retention**. The baseline retention rate is `0.53`, and `d_min = 0.01`. The required number of samples calculated from the online calculator is 39,115. Note that this is the number of users who finished the 14 days free trial, and in order to get that number, we need `(39115 / (0.08 * 0.20625)) * 2 = 4,741,212` page views.

- **Net conversion.**  The baseline conversion rate is `0.1093125`, and `d_min = 0.0075`. The required number of samples calculated from the online calculator is 27,413. Note that this is the number of clicks on "start free trial", and in order to get that number, we need `(27413 / 0.08) * 2` = 685,325 page views.

If we keep the retention rate as a evaluation metric, the number of required pages will be too large (in order to get 4.7 million page views, it takes 117 days of full site traffic, which is not realistic). Therefore we decide to drop the retention rate evaluation metric, and use gross conversion and net conversion as evaluation metrics, and the required number of page views (take the larger one) is 685,325.

## Sizing (Duration vs. Exposure)
Question: Indicate what fraction of traffic you would divert to this experiment and, given this, how many days you would need to run the experiment. 

Answer:

We decide to redirect 50% of the traffic to our experiment, and the length of the experiment is therefore `685,325 / (40000 * 0.5) = 34.3 = 35` days (where 40000 is the baseline number of visitors per day).

解释：The 50% traffic being redirected to the experiment means that 25% will go to control group and 25% to experiment group, and therefore we risk about a quarter of users seeing an not-yet-evaluated feature. This is relatively large risk, because those 25% users will see a different rendering of "start free trial" page which potentially discourages them to start the free trial (although the intention is to increase the overall net conversion). But this relatively large risk is a reluctant choice in order to keep the length of the experiment in a reasonable amount of time. If we reduce the risk by half (sending 12.5% users to see not-yet-evaluted feature), the length will be doubled, taking more than 2 months, which is a little too long.

# Experiment Analysis
## Sanity Checks
Question: For each of your invariant metrics, give the 95% confidence interval for the value you expect to observe, the actual observed value, and whether the metric passes your sanity check. The data could be found at: https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0.

Answer:

For counts ("number of cookies" and "number of clicks"), we model the assignment to control and experiment group as a Bernoulli distribution with probability 0.5. Therefore the standard deviation is `std = sqrt(0.5 * 0.5 / (N_1 + N_2))`, and the margin of error is `me = 1.96 * std`. The lower bound will be `0.5 - me` and the higher bound will be `0.5 + me`. The actual observed value is number of assignments to control group divide by the number of total assignments.

**Number of cookies**

```
control group total = 345543
experiment group total = 344660
standard deviation = sqrt(0.5 * 0.5 / (345543 + 344660)) = 0.0006018
margin of error = 1.96 * 0.0006018 = 0.0011796
lower bound = 0.5 - 0.0011797 = 0.4988
upper bound = 0.5 + 0.0011797 = 0.5012
observed = 345543 / (345543 + 344660) = 0.5006
```
Passed.

**Number of clicks on "start free trial"**
```
control group total = 28378
experiment group total = 28325
standard deviation = sqrt(0.5 * 0.5 / (28378 + 28325)) = 0.0021
margin of error = 1.96 * 0.0021 = 0.0041
lower bound = 0.5 - 0.0041 = 0.4959
upper bound = 0.5 + 0.0041 = 0.5041
observed = 28378 / (28378 + 28325) = 0.5005
```
Passed.

**Click-through-probability on "start free trial"**

For click through probability, we first compute the control value `p_cnt`, and then estimate the standard deviation using this value with experiment group's sample size, i.e. `std = sqrt(p_cnt * (1 - p_cnt) / N_exp)`. The margin of error is 1.96 times of standard deviation.

```
control value = 28378/345543 = 0.0821258
standard deviation = sqrt(0.0821258 * (1-0.0821258) / 344660) = 0.000468
margin of error = 1.96 * 0.000468 = 0.00092
lower bound = 0.0821258 - 0.00092 = 0.0812
upper bound = 0.0821258 + 0.00092 = 0.0830
experiment value = 28325/344660 = 0.0821824
```

The observed value (experiment value) is within the bounds, and therefore this invariant metric passed the sanity check.

## Result Analysis
### Effect Size Tests

### Sign Tests

### Summary

## Recommendation

# Follow-up Experiment

References:
- https://github.com/yxiong/udacity-data-analyst/tree/master/a-b-test
- https://github.com/jasonicarter/DAND_AB_Testing/blob/master/final_project.md
- https://github.com/lyvinhhung/Udacity-Data-Analyst-Nanodegree/blob/master/p7%20-%20Design%20and%20Analyze%20A-B%20Testing%20/README.md
