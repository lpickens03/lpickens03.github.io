---
layout: note
type: Note
category: Notes
tags: [math]
title: Probability
---

Reviews of the mathematical model for determining the "chance" of an event occuring. Note that statistics tends to use $P(A)$ for the probability of an event. However, most cryptology books use $P(X)$ since mathematicians and computer scientists are generally more comfortable with $x$ and $y$. I'll follow the trends of my people here.

<h2>Marginal Probability</h2>
Marginal probability is the probability of an event ($P(x)$) occuring. In the most basic sense, the probability of an event $x$ occuring ($P(x)$) is equal to the number of ways the event $x$ can occur divided by the total number of possible outcomes of the experiment:<br/>

$$P(x) = \frac{\#\;of\;ways\;event\;can\;occur}{\#\;of\;total\;possible\; outcomes}, $$

So the probability of a coin flip resulting in heads is: $P(x) = \frac{1}{2}$. The total possible outcomes in this instance is the sample space $X = (heads,\;tails)$ which has a total of 2 possible outcomes.

It is important to note that $x$ isn't necessarily a unique outcome in $X$. There might be multiple ways to achieve $x$ making $x$ a subset of $X$. For instance, if we wanted to know the probability of rolling an even number on a dice, $x$ = (2,4,6). So $P(x)$ in this case would be $\frac{3}{6}$ or 0.5.

We can formalize this concept by considering a probability distribution $P(x)$. In this case each discrete random variable $x \in X$ has a probability associated with it ($P[X = x]$) such that 

$$0 \le P[X = x] \le 1$$ 

and 

$$\sum_{x \in X} P[X = x] = 1$$ 

You can see this characteristic in our overly simplified coin flip example. Here $X = (heads,\;tails)$ and the probability $P[X = x]$ for each $x$ is 0.5. The sum of all of these probabilities is thus 0.5 + 0.5 = 1.

Similarly, for our dice example, we would calculate the probability of each number being rolled and add them together: $\frac{1}{6} + \frac{1}{6} + \frac{1}{6} + \frac{1}{6} + \frac{1}{6} + \frac{1}{6} = 1$

<h2>Joint Probability</h2>
Now suppose I want to determine the probability that two or more events will occur at the same time. The key here is that both events are independent of each other, i.e. neither event depends on the other to occur first in order to occur themselves. 

So if we are looking at two events, we can define $P[(x,y)]$ as the probability that $X = x$ and $Y = y$. If $X$ and $Y$ are independent random variables, then<br/>

$$P[x,y] = P[x]P[y]$$

So if we roll two dice, we can compute the probability that roll two numbers that equal 5. First we note the possible combinations that equal 4 as [(2,2), (1,3), (3,1)]. Thus, $P[x,y] = \frac{3}{36}$. We get 3 possible ways our event can occur. 36 results from us multiplying the total number of possible outcomes of $X$ and $Y$ which is $6 \times 6$ or 36. 

<h2>Conditional Probability</h2>
Conditional probability determines the likelihood that an event will occur given something else is guaranteed to happen. So we can define condition probabilty $$P[x,y]$$ as the probability that $X = x$ given that $Y = y$. 

With this we can formalize our definition on independence to $X$ and $Y$ are independent if:

$$P[x \vert y] = P[x]P[y]$$, for all $$(x,y)$$ in $$X \times Y$$

So again using our dice example, we would ask what is the probabilty of the sum of two dice being 5, if the first dice we rolled is a 3. In this case, the likelihood would be $\frac{1}{6}$ since there is only one number (2) in the set of 6 that would give us 5. So we can see that the probability of getting the combination we want has been greatly reduced if we can guarantee one of the numbers is rolled.

<h2>Bayes Theorem</h2>
All of this leads us to Bayes Theorem which is used to determine the probability of an event based on knowledge of conditions which may be related:

If $P[y] \gt 0$, then

$$P[x \vert y] = \frac{P[x]P[y \vert x]}{P[y]}$$

So with our dice example, we can use Bayes theorem to find the probability of if an odd number is rolled, it will be 3. We can set $x$ to the probability you will roll a 3 and $y$ to the probability you will roll an odd number:

$$P[x \vert y] = \frac{P[x]P[y \vert x]}{P[y]} = \frac{(\frac{1}{6}) \times 1}{\frac{1}{2}} = \frac{1}{3}$$

So we can see that the likelihood of rolling a 3 ($\frac{1}{6}$) divided by the likelihood of rolling an odd number ($\frac{1}{2}$) gives us our probability. This follows our logic of reducing the set of possible outcomes to the three odd numbers only one of which is 3. So just using marginal probability it would be $\frac{1}{3}$.

<h2>Other Sources</h2>
Khan Academy has a pretty good introduction to basic probability <a href="//www.khanacademy.org/math/precalculus/prob-comb/basic-prob-precalc/v/basic-probability">here</a>.