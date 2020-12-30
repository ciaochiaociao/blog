---
title: Evaluation/IOB Issue
---

1. Error Handling of invalid IOB tagging:
 - e.g. in IOB2 tagging
 ```
 B-LOC I-PER
 ```
 - some convert it to `B-LOC B-PER` (`flair`)
 - some convert it to `B-LOC I-LOC`
 - some convert it to `B-LOC O`
 - some convert it to `O O`
 This makes inconsistent evalution results between `flair`, `conlleval`, `seqeval`, `BERT-NER`, or my package `ner_error_analysis`