---
title: Evaluation/IOB Issue - Error Handling of invalid IOB tagging
---

## Error Handling of invalid IOB tagging:
###  in IOB2 tagging
  - valid:
    ```
    B-LOC B-PER
    B-LOC I-LOC
    ```
  - invalid:
    ```
    O B-LOC I-PER O  # inconsisten type inside entity
    O I-LOC  # not starting with B- for BOE
    ```
 - some convert it to `B-LOC B-PER` (in `flair.iob2` (iob1 to iob2), `seqeval` (used by huggingface))
 - some convert it to `B-LOC I-LOC`
 - some convert it to `B-LOC O`
 - some convert it to `O O`
 This makes inconsistent evaluation results between `flair`, `conlleval`, `seqeval`, `BERT-NER`, or my package `ner_error_analysis`

###  in IOBES tagging
- valid
    ```
    O B-LOC I-LOC E-LOC O
    O S-MISC O
    O B-PER E-PER O
    O S-MISC B-LOC E-LOC S-LOC B-PER E-PER O S-MISC S-MISC O
    ```
- invalid
    ```
    O I-LOC E-LOC O  # not starting with B-
    O B-LOC I-LOC  # not ending with E-
    O B-LOC E-LOC I-PER I-PER E-PER  # not using B- for the PER entity
    O B-LOC I-LOC I-PER I-PER E-PER  # not using B- for the PER entity and not using E- for LOC entity
    O B-/E-/I-PER O  # not using S- for singleton
    O S-MISC I-MISC O  # not using S- for the second MISC OR not using B- for the first MISC
    ```


###  in IOB1 tagging

  - valid:
    ```
    O I-LOC B-LOC O
    O I-LOC I-MISC O
    ```
- invalid:
	```
	O I-LOC B-MISC O  # not necessary for using a boundary prefix, 'B-', for no presence of the consecutive entities of same type
	```

## Scripts

### flair

- `flair` by default uses `BIOES` tagging, to which it converts any other tagging scheme. 
- When converting from other schemes to `BIOES` scheme, `flair` favors the **entity type** information, which means it will create multiple entities upon inconsistent entity types within a BIO span, e.g., (iob2) B-PER I-PER I-ORG O => (bioes) B-PER E-PER S-ORG O
- When extracting span from the tags in `Sentence.get_spans()`, `flair` favors the **BIO** information, e.g., (iobes) B-PER I-PER E-ORG O => three-word PER entity (instead of a PER entity followed by an ORG entity)

ref: https://github.com/flairNLP/flair/pull/1671

> whenever possible, we now use the evaluation methods of sklearn (instead of our own implementations which kept getting issues). This applies to text classification and (most) sequence tagging.
>
> A notable exception is "span-F1" which is used to evaluate NER because there is no good way of counting true negatives. After this PR, our implementation should now exactly mirror the original `conlleval` script of the CoNLL-02 challenge. In addition to using our reimplementation, an output file is now automatically generated that can be directly used with the `conlleval` script.
>
> `SequenceTagger.evaluate()` now explicitly distinguishes between F1 and Span-F1. In the latter case, no TN are counted (closes [#1663](https://github.com/flairNLP/flair/issues/1663)) and a non-sklearn implementation is used.

### seqeval (used by huggingface)

### conlleval (official, and has python version coverted from perl script)

- not handle BIOES but only IOB1/IOB2

ref:https://github.com/flairNLP/flair/issues/298

> alankbik: we understand this to be an error in the original CoNLL-03 evaluation script, which was not designed to handle BIOES tagged entities. When using BIOES tagged entities, there are a few edge cases in which the script will not detect an error. For instance, if there are two S- tagged entities that follow each other. Or an S- followed by a I- and -E.

ref: https://github.com/flairNLP/flair/issues/580

> The CoNLL script will split this into two entities: One one-word entity of type `prod.media` and one two-word entity of type `prod.art`. Our heuristic instead favors the BIO information over the entity type information and thus creates one three word entity of the majority type, in this case `prod.art`.