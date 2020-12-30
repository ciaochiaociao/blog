---
title: Training With Train/Dev
---

Often, when we talk about training the parameters of a model, the first thing come to our mind is: we give the model a gold benchmark and during the training/backpropagation process, the gradient will be calculated for each example in the training set and updated to the parameters.

But actually, we have another choice for training the parameters. Mentioned by Prof. Su in the 248th group meeting on June 17, 2020, not only can we train the paramters with the **training set**, we can also train with the **development set**. This is often referred as **hyperparamters tuning**, **grid search** as well.

1. Train with training set
We use all examples in training set to train all parameters in the model. Intuitively, you can train all parameters of a model during training. But it may have some side effects that if the examples in the training set are unbalanced so as to make some of the parameters/features dominate among all, the other parameters may not get chance of being trained. But by training with development set, we can ensure that the hyperparameters are being **trained**/**tuned** although it is more computationally costly.

3. Train with development set
We set the value for the parameter, or **hyperparameter** to be more specific, for every training experiment. And with a simple grid search over the different combinations of the hyperparameters, we can find the best setting for the hyperparamteres, which can be understood as **training with development set**.