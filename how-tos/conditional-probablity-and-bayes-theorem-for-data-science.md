---
description: >-
  How to approach a real-world problem? Bringing mathematical theorems & data
  together
---

# ❔ Conditional Probablity & Bayes’ Theorem for Data Science

## Table of Contents <a href="#dec3" id="dec3"></a>

> [Conditional Probability](conditional-probablity-and-bayes-theorem-for-data-science.md#26da)
>
> [Problem 1](conditional-probablity-and-bayes-theorem-for-data-science.md#7b16)
>
> [Problem 2](conditional-probablity-and-bayes-theorem-for-data-science.md#38d7)
>
> [The law of total probability](conditional-probablity-and-bayes-theorem-for-data-science.md#123e)
>
> [Problem 3](conditional-probablity-and-bayes-theorem-for-data-science.md#8a74)
>
> [Bayes’ Theorem](conditional-probablity-and-bayes-theorem-for-data-science.md#de1e)
>
> [When to apply Conditional Probability & when to apply Bayes’ Theorem?](conditional-probablity-and-bayes-theorem-for-data-science.md#34d8)

## _Conditional_ Probability <a href="#26da" id="26da"></a>

Okay. You studied Probability in High School & in College. You know how to calculate Probability of a coin toss or the chances of finding a certain colored ball in a bag. You remember the formula _**P(A/B)= P(A ∩ B) / P(B)**._ You remember the definition —

> **The possibility of an event P(A/B) occuring , based on the existence of a previously occured event P(B)**

Great! So, I have a challenge for you.

If you can solve it, feel free to skip this blog and give yourself a pat! But if you can’t solve this problem, no need to worry either. Just give this blog a read and let’s grow together ❤

Here’s the problem —

### **An airport hires you to estimate how the punctuality of flight arrivals is affected by the weather. You begin by defining a probability space for which the sample space is {late and rain, late and no rain, on time and rain, on time and no rain} . From data of past flights you determine that a reasonable estimate for the probability measure of the probability space is P (late, no rain) = 2/20 , P (on time, no rain) = 14/20 , P (late,rain) = 3/20 , P(on time,rain) = 1/20 .** <a href="#7b16" id="7b16"></a>

How would you go about this? Let’s see.

### What is actually our Problem Statement here? <a href="#e22e" id="e22e"></a>

To estimate how the weather affects punctuality of the flights, after taking a look at the provided sample space, we can assume that we need to estimate what is the probability of the flight being late if it’s raining.

### How can we calculate this probability? <a href="#b9a8" id="b9a8"></a>

We have to calculate the probabilty of the flight arrival being late **when it’s raining**. From our provided sample space, two data points that heed this use case are P(late, rain) and P(on time,rain).

Hence, the total probability of it raining is P(late, rain) + P(on time,rain).

And, the probability of the flight being late when it’s raining is P(late,rain).

So the probability of the flight being late when there is rain wil be -

**P (late/rain) = P(late**_**∩ rain**_**) / P(rain) = P(late,rain) / \[P(late, rain) + P(on time,rain)] = (3/20) /\[(3/20) + (1/20)] = 3/4**

I have a follow-up question though.

### Suppose, your aunt is arriving at the airport tomorrow and you would like to know how likely it is for her flight to be on time. From the above example, you recall that P (on time| no rain) = 14/20, P (on time | rain) = 1/20.After checking out a weather website, you determine that P (rain) = 0.2. Now, how can we integrate all of this information to estimate the your aunt’s arrival? <a href="#38d7" id="38d7"></a>

Consider, P (late|rain) = 0.75, P (late|no rain) = 0.125.

We have to calculate the **total probability of aunt’s flight being late**.

## The law of total probability <a href="#123e" id="123e"></a>

Consider three events A, B, and C. Events B and C are distinct from each other (P(rain) and P(no rain)), while event A intersects with both events . We do not know the probability of event A **(P(on time))**. However, we know the probability of event A under condition B **(P(on time | rain))** and the probability of event A under condition C **(P(on time | no rain))**.

The total probability rule states that by using the two conditional probabilities, we can find the probability of event A.

The formula goes like this —

### **P(A) = Σ P (A ∩ S)** <a href="#2075" id="2075"></a>

where, C represents all events A intersects with. In our case , it will be

**P (late) = P (late∩** **rain) + P (late∩** **no rain)**

Just tweaking the formula for conditional probability _P(A/B)= P(A ∩ B) / P(B)_, we get —

### _**P(A ∩ B) =P(A | B) \* P(B)**_ <a href="#869f" id="869f"></a>

Substituting this in the formula for total probability and looking at what information we have so far, we can create a solution this way —

**P (late) = P (late|rain) P (rain) + P (late|no rain) P (no rain)**

We have the total probability of P(rain). Hence, we can calculate total probability of **P(no rain)** i.e 1 — P(rain).

**P (late) = P (late|rain) P (rain) + P (late|no rain) P (no rain) = (0.75)\*(0.2) + (0.125)\*(0.8) = 0.25**

Now, consider this scenario

### You explain above mentioned probabilistic model to your friend. You tell them that your aunt arrived late but you don’t mention if it rained or not. Now, if they want to calculate the probability of it raining, they can’t directly consider P(rain)=0.2 as mentioned in the earlier problem. <a href="#8a74" id="8a74"></a>

This is because we have to take into consideration the new information about the occured event of aunt arriving late to calculate if it rained.

That is where Bayes Theorem comes into the picture.

## Bayes’ Theorem <a href="#de1e" id="de1e"></a>

> Bayes’ theorem provides a way to revise existing predictions or theories given new or additional evidence.

Mathematically, you could represent it as —

### P (A|B) = \[P (A) \*P (B|A)] / P (B) <a href="#e7a0" id="e7a0"></a>

where, B is the additional information to be considered.

So, we have to calculate P(rain | late).

**P (rain|late) = \[P (late|rain) \*P (rain)] / P (late)**

We know that P(rain)=0.2 and P(late) we can calculate as **P(late)=P(late | rain)\*P(rain) + P(late | no rain)\*P(no rain)** = (0.75)\*(0.2)+(0.125)\*(0.8)=**0.25**

Hence,

**P (rain|late) = \[P (late|rain) \*P (rain)] / P (late)=\[**(0.75)\*(0.2)/0.25]**=0.6**

## When to apply Conditional Probability & when to apply Bayes’ Theorem? <a href="#34d8" id="34d8"></a>

In everyday situations, conditional probability is a probability where additional information is already known. Finding the probability of a team scoring better in the next match as they have a former Olympian for a coach is a conditional probability compared to the probability when a random player is hired as a coach.

### References: <a href="#3b9f" id="3b9f"></a>

* [https://cims.nyu.edu/\~cfgranda/pages/stuff/probability\_stats\_for\_DS.pdf](https://cims.nyu.edu/\~cfgranda/pages/stuff/probability\_stats\_for\_DS.pdf)

## Let’s think better together! <a href="#b030" id="b030"></a>
