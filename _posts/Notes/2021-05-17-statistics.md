---
title: Statistics
---

### Bayesian Inference

Bayesian inference derives the [posterior probability](https://en.wikipedia.org/wiki/Posterior_probability) as a [consequence](https://en.wikipedia.org/wiki/Consequence_relation) of two [antecedents](https://en.wikipedia.org/wiki/Antecedent_(logic)): a [prior probability](https://en.wikipedia.org/wiki/Prior_probability) and a "[likelihood function](https://en.wikipedia.org/wiki/Likelihood_function)" derived from a [statistical model](https://en.wikipedia.org/wiki/Statistical_model) for the observed data. Bayesian inference computes the posterior probability according to [Bayes' theorem](https://en.wikipedia.org/wiki/Bayes'_theorem):

$$
P(H\mid E)={\frac {P(E\mid H)\cdot P(H)}{P(E)}}
$$

- H stands for **any *hypothesis*** whose probability may be affected by data (called *evidence* below).
  - Often there are competing hypotheses, and the task is to determine which is the most probable.
- E, **the evidence**, corresponds to new data that were not used in computing the prior probability.
- P(H), **the prior probability**, is the estimate of the probability of the hypothesis H before the data E, the current evidence, is observed.
- P(H|E), **the posterior probability**, is the probability of H given E, i.e., after E is observed. 
  - This is what we want to know: the probability of a hypothesis given the observed evidence
- P(E|H) is the probability of observing E given H, and is called **the likelihood**. 
  - derived from a statistical model for the observed data
  - As a function of E with H fixed, it indicates the compatibility of the evidence with the given hypothesis.
  - The likelihood function is a function of the evidence, E, while the posterior probability is a function of the hypothesis, H.
- P(E) is sometimes termed **the marginal likelihood or "model evidence"**. 
  - This factor is the same for all possible hypotheses being considered (as is evident from the fact that the hypothesis H does not appear anywhere in the symbol, unlike for all the other factors), so this factor does not enter into determining the relative probabilities of different hypotheses.
