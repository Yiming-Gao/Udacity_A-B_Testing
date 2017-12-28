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
要求：For each of your evaluation metrics, indicate whether you think the analytic estimate would be comparable to the the empirical variability, or whether you expect them to be different (in which case it might be worth doing an empirical estimate if there is time). Briefly give your reasoning in each case.

For Bernoulli distribution with probability p and population N, the analytical standard deviation is $std = \sqrt{\frac{p*(1-p)}{N}}$.



