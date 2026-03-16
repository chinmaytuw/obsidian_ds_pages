---
Created: 2024-10-24T15:33
Source: https://www.coursera.org/specializations/mathematics-for-machine-learning-and-data-science
---
# [[Conditional Probability]]

- The following example is quite fundamental. We have looked at product rule for independent events previously.
- Here, for the second part, we are looking at an event where `P(sum=10 | 1st=6)` . It’s not just that the sum is 10 but we have to account for the condition where 1st roll is 6. We have fixed the outcome of 1st roll to 6.

  

![[image 4 2.png|image 4 2.png]]

  

## General product rule:

![[Math/images/image 1 1.png|image 1 1.png]]

  

  

# [[Bayes Theorem]]

- Consider a population size of 1million. A disease that occurs in 0.0001% of population. A test effectivity of 99%. Lastly, assume a positive test. We need to find out probability that one is actually sick and not false. Below are the numbers for this configuration.
- people affected by the the disease. so p(disease)

![[Math/images/image 2 1.png|image 2 1.png]]

- Distribution between sick and not sick and diagnosed positive and negative.

![[Math/images/image 3 1.png|image 3 1.png]]

- Probability that one is sick given they are diagnosed positive.

![[Math/images/image 4 1.png]]

- This is ==not intuitive== at first [sight.](http://sight.It) It is primarily driven by the statement that the test is ==99% effective==. But that statement fails to account for the occurrence of the disease in the larger population. The first instinct with the statement 99% effective is ==**P(diag=sick | sick)**== which is actually 99% here. **HOWEVER**, the question we are trying to answer is ==**P(sick | diag=sick)**==. So its around what you are assuming to be the prior. The thing YOU KNOW is that you are diagnosed sick and not that you ARE sick.
- An excel version of the this problem is below.

![[Math/images/image 5 1.png]]

- The key thing to note is the disease occurrence rate which drastically dictates the final result. Here. the rate is 0.0001%. If we switch it to 1%, this is what we get.

![[Math/images/image 6 1.png]]

![[/bayes.xlsx|bayes.xlsx]]

- ==**So a lot depends on the disease occurrence rate in the general public**==.

## **Bayes Theorem - Mathematical Formula**

1. So the problem statement is as such. We need to find probability of sick given we are diagnosed sick. Below are the components. The numerator is quite straightforward given our general product rule from earlier.

![[Math/images/image 7 1.png]]

1. The ==denominator== is a little tricky so let’s look at the tree of possibilities.

![[image 8 1.png]]

1. A side note, each outcome above is a multiplication of individual components. So if we look at the very first outcome, it’s the multiplication of 100 and 0.99

![[image 9 1.png]]

  

1. For diagnosed sick, we need to look two groups like below.

![[image 10 1.png]]

  

1. So taking this into account, the denominator becomes:

![[image 11 1.png]]

  

![[image 12 1.png]]

  

1. xThis boils down to:

![[image 13 1.png]]

  

# Prior, Event, Posterior

1. This can be thought of of in the following terms as well. Consider a case where 20% of emails are spam. 14 of 20 spam have the word lottery in it. 10 of 80 hams have lottery in it.

![[image 14 1.png]]

1. In this case, the basic piece of info we have is 20% of emails are spam. This is the most basic piece of info even before considering lottery. This is the prior. The goal of bayes is to update the prior given the new piece of info or event.
2. Here, the event is email contains the word lottery.
3. The posterior in that case would be P(spam|lottery). Here, we have narrowed down our sample space to account for the new piece of info which is “_14 of 20 spam have the word lottery in it. 10 of 80 hams have lottery in it._”

  

# [[Naive Bayes]]

1. In case of 2 events, we have the following config.

![[image 15 1.png]]

1. This sounds manageable for 2 events. Assume if there were 100 events. Do be able to calculate p for 100 events is much trickier. Maybe there are no events where all 100 words appear in which case the p will be 0. Which is not good enough. So we tend to estimate the p in such cases.

![[image 16 1.png]]

1. In it’s most simplest form, we assume that the 2 words are independent. Which we know isn’t true in real life. For example, it’s fairly obvious that the words good and morning are not independent of each other. The word good will impact p of morning since they appear together often. But if we assume they are independent, the math is more manageable. ==**This framework is thus called “naive” bayes**==.

![[image 17 1.png]]

1. Consider the below scenario. We look at occurrences of words lottery and winning.

![[image 18 1.png]]

![[image 19 1.png]]

  

1. The math flows as follows.

![[image 20 1.png]]