---
title: Add dictionaries to Stanford CoreNLP NER
---

## Add entity / tokens dictionary (Doing)

### File Structure

```
stanford-corenlp-full-2018-10-05
 |
 |--- StanfordCoreNLP-chinese.properties
 |--- fgc-regexner.txt
 |--- fgc-segmenter.txt
 |--- wiki_ner_tag.txt
 |--- wiki_ner.txt
 |--- ddbc-person-regexner.txt
 |--- ddbc-person-segmenter.txt
 |--- cbdb-person-segmenter.txt
 |--- cbdb-person-regexner.txt
```

### Method

```
#  StanfordCoreNLP-chinese.properties
...
segment.serDictionary = edu/stanford/nlp/models/segmenter/chinese/dict-chris6.ser.gz,ddbc-person-segmenter.txt,cbdb-person-segmenter.txt
ner.fine.regexner.mapping = ...,wiki_ner_tag.txt
or
ner.additional.regexner.mapping = ddbc-person-regexner.txt,cbdb-person-regexner.txt
...

# wiki_ner_tag.txt
rms     PERSON
Thales  PERSON
Canton  CITY
BoA     PERSON
Matrix  WORK
NCBI    ORGANIZATION
NCBI    ORGANIZATION
Foucault        PERSON
73      PERSON
BabelPad        PERSON

# wiki_ner.txt
rms
Thales
Canton
BoA
Matrix
NCBI
NCBI
Foucault
73
BabelPad
```


### Test
```
# -- java--
docker attach corenlp
#working1
java -cp '*' -Xmx10g edu.stanford.nlp.pipeline.StanfordCoreNLP -props StanfordCoreNLP-chinese-original.properties -annotators tokenize,ssplit,pos,lemma,ner -file example.txt -outputFormat conll -ner.additional.regexner.mapping fgc-regexner.txt -segment.serDictionary edu/stanford/nlp/models/segmenter/chinese/dict-chris6.ser.gz,fgc-segmenter.txt

#working2
java -cp '*' -Xmx10g edu.stanford.nlp.pipeline.StanfordCoreNLP -props StanfordCoreNLP-chinese-fgc.properties -annotators tokenize,ssplit,pos,lemma,ner -file example.txt -outputFormat conll

#server
java -mx8g -cp '*' edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000 -annotators tokenize,ssplit,pos,lemma,ner,entitylink -serverProperties StanfordCoreNLP-chinese-fgc.properties

#text
马可· 波罗（Marco Polo，又译马可· 孛罗、马哥· 波罗、马哥孛罗，1254年9月15日－1324年1月8日）是威尼斯共和国商人、旅行家及探险家。
```
test results: **GUI does not output the same as python client/java**

### Prof. Wang's Data
source: 中央研究院數位文化中心 鏈結開放資料平台 data.ascdc.tw
![中央研究院數位文化中心 鏈結開放資料平台]({{site.base_url}}/assets/images/ascdc.JPG)


### Add ddbc and cbdb dicts
```
docker attach corenlp
java -mx8g -cp '*' edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000 -serverProperties StanfordCoreNLP-chinese-fgc.properties
java -mx8g -cp '*' edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000 -serverProperties StanfordCoreNLP-chinese-fgc.properties &> run.log
java -mx8g -cp '*' edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000 -serverProperties StanfordCoreNLP-chinese.properties
```

1. Add places, people
before
![](http://localhost:4000/blog/assets/images/dict_before.JPG)

after
![](http://localhost:4000/blog/assets/images/dict_after.JPG)

2. Add reginal titles
![](http://localhost:4000/blog/assets/images/dict2.JPG)


### Add dictionaries from wiki

#### List all Taiwanese people in Wiki
```

select ?s ?sLabel ?sAltLabel ?sitelinks where {
  ?s wdt:P31 wd:Q5;
     wdt:P27|(wdt:P19/wdt:P17*) wd:Q865.
  Optional {?s wikibase:sitelinks ?sitelinks. }

  SERVICE wikibase:label { bd:serviceParam wikibase:language "zh" }
}

```

#### Add regnal titles (TODO)
- Wiki (TODO)
```
select ?s ?sLabel ?sAltLabel ?sitelinks where {
  ?s wdt:P31/wdt:P279* wd:Q256408;
  Optional {?s wikibase:sitelinks ?sitelinks. }

  SERVICE wikibase:label { bd:serviceParam wikibase:language "zh" }
}
```

- From Professor Wang (done)

#### Add Dynasties (TODO)
Dynasties `dynasties-duration.txt`
```
select ?s ?sLabel ?sAltLabel ?sitelinks where {
  ?s wdt:P31/wdt:P279* wd:Q164950;
  Optional {?s wikibase:sitelinks ?sitelinks. }

  SERVICE wikibase:label { bd:serviceParam wikibase:language "zh" }
}
```

Chinese Dynasties (subset of dynasties) `chinese-dynasties-duration.txt`
```
select ?s ?sLabel ?sAltLabel ?sitelinks where {
  ?s wdt:P31/wdt:P279* wd:Q12857432;
  Optional {?s wikibase:sitelinks ?sitelinks. }

  SERVICE wikibase:label { bd:serviceParam wikibase:language "zh" }
}
```

#### Add ancient titles (TODO)

Only use its subject
	- `DQOT_DH_LOD_FORMAT_date_20200203163730`
	- `DQOT_DH_LOD_FORMAT_identifier_20200203163730`
	- `DQOT_DH_LOD_FORMAT_positionRank_20200203163730`

`DQOT_DH_LOD_FORMAT_isPartOf_20200203163731`
	- subject is part of object
	- subject: title
	- object: org
`DQOT_DH_LOD_FORMAT_altLabel_20200203163731`
	- subject has alias object
`DQOT_DH_LOD_FORMAT_isRelatedTo_20200203163731`
	- subject is related to object
	- subject: title
	- object: org, or maybe others?
`DQOT_DH_LOD_FORMAT_positionRank_20200203163730`
	- e.g. 從五品...
`DQOT_DH_LOD_FORMAT_prefLabel_20200203163730`
`DQOT_DH_LOD_FORMAT_relatedPerson_20200203163731`
	- title -> person
`DQOT_DH_LOD_FORMAT_sameAs_20200203163731`
`DQOT_DH_LOD_FORMAT_spatial_20200203163731`
	- title -> org
	- title is a full-name title, e.g. 廣州刺使 instead of only 刺使
	
#### List all Taiwanese companies in Wiki

```

select ?s ?sLabel ?sAltLabel ?sitelinks where {
  ?s wdt:P31/wdt:P279* wd:Q43229;
#      wdt:P17|((wdt:P159|wdt:P740|wdt:P2541)/wdt:P17*) wd:Q865.  # timeout
     wdt:P17 wd:Q865.
  Optional {?s wikibase:sitelinks ?sitelinks. }

  SERVICE wikibase:label { bd:serviceParam wikibase:language "zh" }
}

```


#### List all ancient chinese people in Wiki (`old-person-*.txt`)
```
select ?s ?sLabel ?sAltLabel ?sitelinks where {
  ?s wdt:P31 wd:Q5;
     wdt:P27/wdt:P17*/wdt:P31+ wd:Q12857432.
  Optional {?s wikibase:sitelinks ?sitelinks. }

  SERVICE wikibase:label { bd:serviceParam wikibase:language "zh" }
}
```

Before
![]({{site.baseurl}}/assets/images/old_people_before.JPG)
After
![]({{site.baseurl}}/assets/images/old_people_after.JPG)


#### Use PageRank/Sitelinks
```
PREFIX vrank:<http://purl.org/voc/vrank#>

select ?sLabel ?sAltLabel ?sitelinks ?srank where {
?s wdt:P31 wd:Q5;
   wdt:P27|(wdt:P19/wdt:P17+) wd:Q865;
#    wikibase:sitelinks ?sitelinks.
#   FILTER (?sitelinks < 3)
  MINUS {
    SERVICE <http://dbpedia.org/sparql> {?s vrank:hasRank/vrank:rankValue ?srank } .
  }
#   FILTER NOT EXISTS { ?srank wdt:P31 ?x} .
  
  SERVICE wikibase:label { bd:serviceParam wikibase:language "zh" }
}
# ORDER BY ASC(?srank)
```

### Remove Place Names With Length Two For TGAZ
![]({{site.baseurl}}/assets/images/tgaz_place_len_ge_2.JPG)
![]({{site.baseurl}}/assets/images/tgaz_place_len_ge_3.JPG)

### Examples
Examples that are better:
```
D160
D120
D002
```

Examples that are worse:
```
D182
D004
```

Examples that are better and worse in part:
```
D238
```

## Overrtiing issue (TODO)
ref: https://github.com/stanfordnlp/CoreNLP/issues/983
D001
苏文忠 公 全集
赤壁 赋