---
title: Batch VS Mini-Batch VS Micro-Batch (Mini-Batch Chunk Size) (Batch Accumulation)
---



- Epoch means one pass over the full training set
- Batch means that you use all your data to compute the gradient during one iteration.
- Mini-batch means you only take a subset of all your data during one iteration.

from practical tasks, minibatch should just be called batch, every body is calling it so. even Keras is calling it batch_size instead of minibatch_size



One epoch typically means your algorithm sees **every** training instance once. Now assuming you have nn training instances:

If you run batch update, every parameter update requires your algorithm see each of the nn training instances exactly once, i.e., every epoch your parameters are updated once.

If you run mini-batch update with batch size = bb, every parameter update requires your algorithm see bb of nn training instances, i.e., every epoch your parameters are updated about n/bn/b times.

If you run SGD update, every parameter update requires your algorithm see 1 of nn training instances, i.e., every epoch your parameters are updated about nn times.

My Note:

**micro-batch size** or **mini-batch chunk size** is instead used in `flair` NLP framework, which means the number of the training instances used for calculating and populating gradients, and makes **mini-batch size** become the **batch accumulation** size, which is the number of the training instances used for updating parameters.

(c.f. https://gist.github.com/ciaochiaociao/ad51ddfc971b6b22ff0188b503e16348)