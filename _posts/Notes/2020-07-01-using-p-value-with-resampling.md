---
title: Using P-value with Resampling
---

Date: 2020/7/1
Presenter: Amanda Wu

Parameters: Resampling Rate, Resampling Times



Backgrounds:
 - permutation tests, also called exact tests, randomization tests, or re-randomization tests
	 - Exchanging labels on data points when performing significance tests
 - statistically significant 
	 - In statistical hypothesis testing,[1][2] a result has statistical significance when it is very unlikely to have occurred given the null hypothesis.
	 - significance level, denoted by {\displaystyle \alpha }\alpha , is the probability of the study rejecting the null hypothesis, given that the null hypothesis were assumed to be true;
	 - the p-value of a result, {\displaystyle p}p, is the probability of obtaining a result at least as extreme, given that the null hypothesis were true.
	 - The result is statistically significant, by the standards of the study, when {\displaystyle p\leq \alpha }{\displaystyle p\leq \alpha }

p < alpha: reject null hypothesis
p > alpha: accept null hypothesis
null hypothesis => 巧克力確實有70克的花生/performance沒有比較好

Summary: 
 - If the performance difference is not statistically significant (if p-value is larger than 0.05), use resampling. And the larger the resampling times, the less the p-value.