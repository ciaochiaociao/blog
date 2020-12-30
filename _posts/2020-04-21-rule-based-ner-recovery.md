---
title: Rule-based NER Recovery
---

## Majority Voting With Syntax/NP Information

- What is this experiment: Take NP as potential EMs to recover false errors by using syntax tree
- Why:
    - to find potential entity mentions to recover false errors
        
- Status: Forsaken
    - Reason: No overlapping between the entity mention to be recovered and the found NP
    - Conclusion/Next Step Ideas: 
        - Use NNP instead
        - Expand to/Build a confusion set from the entity mention to be recovered instead
        - Partial match of the original entity mention
- How/What did I do:
    1. Ported CoreNLP statistical parser
    2. Finished adding tree information to sentences, tokens with nltk data structure `Tree`
    3. Finished extracting NPs in the tree at max of 1 level up from every tokens
    4. Fixed add-tree bug of "." in corenlp sspliter
        - Sol Now: Take Conll sentence split as the input without doing further sentence splitting

- What did I not do yet:
    - 3.1 Add NP to sentences, tokens with nltk data structure



## Majority Voting With Clustering - Using BERT Clustering
- What is this experiment: Clustering of entity mentions along with its local context using BERT
- Why: Use the contextual embeddings of the tokens of an entity mention and the tremendous ability to identify the correct \alert{word sense}
    - Benefits:
        - Clustering similar entity mentions with different expressions (paraphrased entity mention)
        - Avoid clustering ones with same surface forms
- Status: Pending/To be decided if we still want to do this
- Results: 
    - World Cup - Asian Cup
    - Location Names