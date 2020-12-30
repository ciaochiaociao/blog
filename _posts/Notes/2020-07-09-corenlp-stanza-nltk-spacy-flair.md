---
title: CoreNLP/Stanza & NLTK & SpaCy & Flair & AllenNLP
---

FLAIR:
 - Sequence Tagger
 - NER
 - POS
 - CHUNK
 - Embeddings
	 - flair contextual string embedding (CSE)
	 - huggingface BERT/ELMO/ ...
	 - Sentence Embedding
	 - Document Embedding

SpaCy:
 - depparse
 - tokenization
 - sentence splitting
 - visualize depparse/NER in browser
 - spacy_stanza

Stanza (Pytorch):
- tokenization
- sentence splitting
- multiword tokenization (mwt)
- NER
- depparse (pytorch)

Stanford CoreNLP (Java):
- depparse
- ner
- constituency parse
- IE
- ...


NLTK
- Parse Tree Data Structure and Visualization
- Parsing Constituency Tree Using CFG
	- Bllip
	- Chart
	- Earley Chart nltk.parse.earleychart (incremental chart parsers)
	- CoreNLP nltk.parse.corenlp
		- CoreNLPDependencyParser
		- CoreNLPParser(url=..., tagtype=...)
			- .parse()
			- .tag()
			- tagtype='ner'
			- tagtype='pos'
	- Malt Dependency Parser nltk.parse.malt
	- Probabilitstic Parser nltk.parse.pchart
	- Recursive Descent (top-down CFG)
	- shift-reduce
	- Stanford nltk.parse.stanford
		- StanfordDependencyParser (englishPCFG)
		- StanfordNeuralDependencyParser
	- transitionparser

AllenNLP
 - Built on PyTorch and spaCy

BEkerley NEural PARser (BENEPAR)
- neural constituency python
- uses tensorflow for production and pytorch for training
- needs tensorflow v1
- has interface to nltk and spacy