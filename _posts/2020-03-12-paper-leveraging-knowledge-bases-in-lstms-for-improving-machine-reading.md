---
title: "[Paper] Leveraging Knowledge Bases in LSTMs for Improving Machine Reading"
---

Task: Entity and Event Extraction
Dataset: ACE05
Model: KBLSTM (LSTM + attention + sentinel + KB embedding )
Used KB: WordNet and NELL (Mitchell, CMU)
KB Training Method: Bilinear (Yang, 2015)
Author: Yang and Mitchell
Published on: ACL 2017 https://www.aclweb.org/anthology/P17-1132.pdf

## About attention with a knowledge sentinel 
- an attention mechanism with a sentinel to adaptively decide whether to attend to background knowledge and which information from KBs is useful. 
- we introduce a sentinel component in BiLSTMs that allows flexibility in deciding whether to attend to background knowledge or not.
- records the information of the current context and use a mixture model to allow for better tradeoff between the impact of background knowledge and information from the context.
![]({{site.baseurl}}/assets/images/LSTM+atn+sentinel+bilinearKBEmbed.JPG)

## Bilinear Transformation
Used in 2 Places
1. Attention calculation
	- Calculate cross-attentions between the concepts and the current LSTM hidden state
	![]({{site.baseurl}}/assets/images/bilinear-atn-for-sentinel.JPG)
	- Calculate cross-attentions between the sentinel and the current LSTM hidden state
	![]({{site.baseurl}}/assets/images/bilinear-atn-for-concept.JPG)
2. KB embedding
	- calculate the score of the relevance of the triple:does subject have relation with object?
	![]({{site.baseurl}}/assets/images/bilinear-triple-score.JPG)

## Sentinel Vector
![]({{site.baseurl}}/assets/images/KBLSTM-sentinel.JPG)

## Overall KBLSTM
![]({{site.baseurl}}/assets/images/KBLSTM1.JPG)
![]({{site.baseurl}}/assets/images/KBLSTM2.JPG)