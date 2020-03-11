---
title: Wiki-based Answer Module
---

# KBQA for FGC
## KBQA
- Given Question & KB, predict the answer

### Question Types
- SFQ
	- s-p-? (we only focus on this)
	- ?-p-o
	- s-?-o
- MFQ (s-p-o-p-o-...)
	- s-p-o-p-o
		- s-p-o-p-?
		- ?-p-o-p-o
		- s-p-?-p-o
	- s->p->o<-p<-s
	- o<-p<-s->p->o
- s->p->? can be found by  ?-> ~p -> s. These two way come from two different nodes/subject

### KBQA Approach Types
- rule-based
- keyword-based
- synonym-based
- template-based (we use this)

## Traditional KBQA vs KBQA for FGC
 - KBQA in FGC has not only the KB and questions, but also the passages
 - **The answers/info to answer should come from passage!!**
 - Some questions that can be answered from KB should return "资讯不足无法判断" IF the question can not be answered purely from the passage
 - KBQA normally provides inference path in their training data while the inference path should be manually annotated for FGC.

## Targets
 - High precision is preferred to recall
 - Answers that can not be answered might be able to be answered by other answer modules. WikiQA for FGC is like *lottery*. Hard to match one. But once matched, it has to be most likely correct
 - This answer module is called for every question. If WikiQA has answers, it returns its answer. Otherwise, just return None/empty answer.

## Requirements
- Stanford CoreNLP server
- Wikidata MongoDB server

## Overview

0. Preprocessing
 (rule) only deal with 'Single-Span Extraction' & 'Date-Duration'
2. Parsing Question
	(rule) match with regex
	- s/o surface form
	- predicate inference
2. Build Candidates to Entity Linking
	- (rule) Add the s/o surface form to the candidates list
	- (rule) Add NEs overlapped with the s/o surface form to the candidates list
	- (rule) Remove '.' if the NE contains '.' (now split by '.') 
3. Entity Linking
	- (rule) Match the candidates with the labels/aliases of Wikidata item 
	- (rule) Match NE type with Wikidata item type (to do)
5. Traverse KB
6. Post-process values
	- (rule) time, quantity, wikibase-item
	- (rule) trad2sim
	- (rule) filter out values of wikibase-item with only one character
	- (rule) do not filter out values of wikibase-item with only one character when the attribute name is '朝代'
8. Coordinating with passage
	- (rule) fuzzy-match/match labels/aliases of wikibase-item with passage
	- (rule) NE expansion/diminishing
	- (rule) do not use NE expansion/diminishing when attribute name is '朝代' 
	- (rule) NE and ANS type match
	- (rule) '朝代' in qtext and ans_type in ent_ans_map['COUNTRY'] + ent_ans_map['TIME']
9. Final decision 
  - (rule) answer with longest length

## FGC question types
```
# 朝代
['D001Q01', 'D001Q06', 'D001Q09', 'D254Q01', 'D274Q01', 'D275Q01', 'D275Q01', 'D285Q01', 'D293Q01', 'D302Q01', 'D305Q01', 'D305Q06', 'D305Q08']
```