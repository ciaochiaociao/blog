---
title: Tweak CoreNLP for FGC
---

## Fix properties typo in starting CoreNLP server via `java` (`-props` vs `serverProperties`)

Properties file: `StanfordCoreNLP-chinese.properties`

Old:

```
# 51 
docker attach corenlp
java -mx8g -cp '*' edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000 -annotators 'tokenize,ssplit,pos,lemma,ner,regexner' -props StanfordCoreNLP-chinese.properties
```

Fixed:
```
# 51 
docker attach corenlp
java -mx8g -cp '*' edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000 -serverProperties StanfordCoreNLP-chinese.properties
```


## Add entity linking (To Do)
```
# in Server: StanfordCoreNLP-chinese.properties
annotators = ..., entitylink 
# in Client (python)
nlp.annotate(..., properties={'annotators': '...,entitylink'})
# in Client (terminal wget)

# directly
java -mx8g -cp '*' edu.stanford.nlp.pipeline.StanfordCoreNLP -annotators tokenize,ssplit,pos,lemma,ner,entitylink -file example.txt -outputFormat text
java -mx8g -cp '*' edu.stanford.nlp.pipeline.StanfordCoreNLP -props StanfordCoreNLP-chinese.properties  -annotators tokenize,ssplit,pos,lemma,ner,entitylink -file example.txt -outputFormat text

# output
[Text=他们 CharacterOffsetBegin=350 CharacterOffsetEnd=352 PartOfSpeech=PN Lemma=他们 NamedEntityTag=O WikipediaEntity=O]
[Text=从 CharacterOffsetBegin=352 CharacterOffsetEnd=353 PartOfSpeech=P Lemma=从 NamedEntityTag=O WikipediaEntity=O]
[Text=威尼斯 CharacterOffsetBegin=353 CharacterOffsetEnd=356 PartOfSpeech=NR Lemma=威尼斯 NamedEntityTag=CITY WikipediaEntity=Venice]
[Text=乘船 CharacterOffsetBegin=356 CharacterOffsetEnd=358 PartOfSpeech=VV Lemma=乘船 NamedEntityTag=O WikipediaEntity=O]
[Text=到 CharacterOffsetBegin=358 CharacterOffsetEnd=359 PartOfSpeech=VV Lemma=到 NamedEntityTag=O WikipediaEntity=O]
[Text=黑海 CharacterOffsetBegin=359 CharacterOffsetEnd=361 PartOfSpeech=NR Lemma=黑海 NamedEntityTag=LOCATION WikipediaEntity=Black_Sea]
```



## Fix error in ssplit
 - Commit 
	 - https://github.com/ciaochiaociao/WikiQA/commit/ec6edc35ae6e433f2d4435fe70a05dd88574c954
 - related issues
	 - https://github.com/stanfordnlp/CoreNLP/issues/897

Note we change the default:

```
#change from
ssplit.boundaryTokenRegex = [.。]|[!?！？]+
#to
ssplit.boundaryTokenRegex = [。]|[!?！？]+
```

### Note1 - Adding ssplit for Chinese in server end does not work

Adding `ssplit.boundaryTokenRegex = [。]|[!?！？]+` to `StanfordCoreNLP-chinese.properties` with `serverProperties` set does not work.

### Note2 - Adding ssplit for Chinese in **client** end

```python
from stanfordnlp.server import CoreNLPClient
nlp = CoreNLPClient(...)
nlp.annotate(text, properties={ ... 'ssplit.boundaryTokenRegex': '[。]|[!?！？]+'})
```