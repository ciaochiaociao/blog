---
title: Classification Model (Old)
---

## Build the data flow of Classification Model


Candidate Scoring + Softmax Model References:
- Text classification with TF-Hub (no keras)
 https://colab.research.google.com/github/tensorflow/hub/blob/master/docs/tutorials/text_classification_with_tf_hub.ipynb#scrollTo=25rdoEHih0fm

- Classifier with TF-HUB (BERT) (no keras)

https://github.com/google-research/bert/blob/master/run_classifier_with_tfhub.py

- Classifier with TF (BERT) (no keras)
[code](https://github.com/google-research/bert/blob/master/run_classifier.py)
[description](https://github.com/google-research/bert#sentence-and-sentence-pair-classification-tasks)

- Text Classification with TF-HUB (Keras) https://github.com/tensorflow/docs/blob/master/site/en/tutorials/keras/text_classification_with_hub.ipynb

- Text Classification (Keras)
[https://www.tensorflow.org/tutorials/text/text\_classification\_rnn](https://www.tensorflow.org/tutorials/text/text_classification_rnn)




- Multiple-choice (SWAG, Pytorch, huggingface transformers)
[https://huggingface.co/transformers/examples.html#fine-tuning-on-swag](https://huggingface.co/transformers/examples.html#fine-tuning-on-swag)

- Paraphrase (GLUE, Pytorch, huggingface transformers)
[https://huggingface.co/transformers/examples.html#tensorflow-2-0-bert-models-on-glue](https://huggingface.co/transformers/examples.html#tensorflow-2-0-bert-models-on-glue)

- Keras + huggingface transformers
[https://mc.ai/bert-in-keras-tensorflow-2-0-using-tfhub-huggingface/](https://mc.ai/bert-in-keras-tensorflow-2-0-using-tfhub-huggingface/)


Embedding:
- Universal Sentence Encoder, Multilingual Universal Sentence Encoder for Semantic Retrieval (Transformer, Deep Averaging Network, CNN) 
[Universal Sentence Encoder pdf](https://arxiv.org/pdf/1803.11175.pdf), [Multilingual Universal Sentence Encoder for Semantic Retrieval pdf](https://arxiv.org/pdf/1907.04307.pdf), [code](https://tfhub.dev/google/collections/universal-sentence-encoder/1)

- Transformers (huggingface)
[code](https://github.com/huggingface/transformers)


### Scorer (FFN)