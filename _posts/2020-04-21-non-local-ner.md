---
title: Non-local NER
---

- Selected Models
	- BERT
		- subword-level Transformers
	- flair 
		- Character-level BiLSTM + CRF
	- less powerful models
		- traditional BiLSTM + CRF models (word-level)
- Selected Datasets
	- WNUT17
		- Noisiser
		- No document splits
		- Tweets, YouTube Comments, StackExchange, Reddit
		- creative-work, group, product, person, organization, location
		- BIO1 (B for beginning)
	- ACE05
		- News
		- PER, ORG, GPE(Geo-Political Entity), FAC, LOC, WEA, VEH
		- Also tags pronouns though
			- Maybe it's just [me]PER, but [I]PER think [Michael Jackson]PER, [he]PER 's kooky, wacky .
		- BIO1 (B for beginning)cd 
		- More Info
			- FP -> ADJ (QC)
			  FP /
				- FP -> SP -> ADJ + QC
				- FP1: First Pass
				- FP2: Dual First Pass
				- ADJ: Adjudication
				- NORM: TIMEX2 normalizatio
			- at least 50 examples of each
type/subtype
	- CoNLL03
		- News
		- PER, ORG, LOC, MISC
		- BIO2 (B for boundary)