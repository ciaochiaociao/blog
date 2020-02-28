---
title: FGC dataset benchmark generation
---

## benchmark
 input: question_text of SFQ

 ```txt
苏东坡在中国历史上，是哪一个朝代的人？ ['北宋']
苏东坡是中国哪个省份的人？ ['四川省']
苏东坡的爸爸叫什么名字? ['苏洵']
苏文忠公指的是谁? ['苏轼']
 ```

 output: answer, subject-predicate-object
 (use QID, PID if possible)
 (use '?' to denote the question)

 ```python
苏东坡在中国历史上，是哪一个朝代的人？ ['北宋'], Q36020-P27-?x
苏东坡是中国哪个省份的人？ ['四川省'], Q36020-N/A-?x
苏东坡的爸爸叫什么名字? ['苏洵'], Q36020-P22-?x
苏文忠公指的是谁? ['苏轼'], ?x-alias-苏文忠
 ```

## Generation

Target: extract out SFQ - single factoid question (1-hop)

Input: SFQ and its answer
Output: pairs of (question_text, QID, PID)

## Generate by SPARQL
Given S, O, find P

### when S, O are all wikibase-item
#### errors

* EndPointInternalError: D255Q02, D312Q04, D215Q02, D210Q03, D115Q05, D256Q01, D013Q01, D069Q03
* QueryBadFormed: D256Q01

#### results

```.tsv
[D001Q01]苏东坡 在 中国 历史 上 ， 是 哪 一 个 朝代 的 人 ？苏轼-國籍-北宋
[D001Q03]苏东坡 的 爸爸 叫 什么 名字 ?苏轼-父亲-苏洵
[D001Q06]韩愈 在 中国 历史 上 ， 是 哪 一 个 朝代 的 人 ？韩愈-國籍-唐代
[D004Q06]苏东坡 在 哪个 城市 过世 ?苏轼-死亡地-常州
[D064Q04]郭金火 在 16 岁 的 时候 拜 谁 为师 ？郭金火-老師-蔡雪溪
[D064Q07]郭雪湖 的 妻子 是 谁 ？郭雪湖-配偶-林阿琴
[D069Q01]李安 的 出生地 在 哪里 ? 李安-國籍-台湾 ['台湾屏东潮州', '台湾', '台湾屏东', '屏东潮州', '屏东', '潮州']
[D069Q02]李安 的 职业 是 什么 ? 李安-职业-导演
[D071Q13]张大千 在 哪里 去世 ? 张大千-死亡地-台北
[D072Q04]莎士比亚 的 出生地 在哪 ? 莎士比亚-出生地-雅芳河畔斯特拉特福 ['英格兰沃里克郡雅芳河畔斯特拉特福', '英格兰', '沃里克郡', '雅芳河畔斯特拉特福']	
[D072Q06]莎士比亚死于何地? 莎士比亚-出生地-雅芳河畔斯特拉特福
[D092Q10]台湾官方使用的中文是「简体字」还是「繁体字」?台湾-書寫系統-繁体字
[D092Q11]香港官方使用的中文是「简体字」还是「繁体字」?香港-書寫系統-繁体字
[D092Q12]新加坡官方使用的中文是「简体字」还是「繁体字」?新加坡-書寫系統-简体字 ['简体字', '简体中文']
[D093Q07]台湾地区使用的中文是何种字体?台湾-書寫系統-繁体字	['繁体', '繁体字', '传承字', '正体字', '原体字', '深笔字']	
[D096Q07]《精灵宝可梦》系列中最具代表性的宝可梦是哪一个? 皮卡丘-登场作品-精灵宝可梦
[D097Q01]诺贝尔奖是根据哪位学者的遗嘱而设立的? 诺贝尔奖-創辦者-诺贝尔 ['瑞典化学家阿佛烈·诺贝尔', '阿佛烈·诺贝尔', '诺贝尔']
[D214Q01]华盛顿纪念碑是纪念哪一位美国总统? 华盛顿纪念碑-得名于-乔治·华盛顿
```



e.g. 苏东坡是哪一個朝代的人? 北宋
```
select ?sLabel ?plabel ?oLabel where {
?s ?p ?o .
?s rdfs:label|skos:altLabel '苏东坡'@zh .
?o rdfs:label|skos:altLabel '北宋'@zh .
?pi ?ref ?p .
?pi rdfs:label ?plabel FILTER (lang(?plabel) = "zh")
SERVICE wikibase:label { bd:serviceParam wikibase:language "zh" }
}
```


### when one of S or O is time/date
e.g. 苏东坡 出生 于 哪 一 年 ?['1037年']
```
select ?sLabel ?plabel ?o where {
?s ?p ?o .
?s rdfs:label|skos:altLabel '苏东坡'@zh .
FILTER (datatype(?o) = xsd:dateTime) .
FILTER (year(?o) = 1036 || year(?o) =1036 + 1911)
?pi ?ref ?p .
?pi rdfs:label ?plabel FILTER (lang(?plabel) = "zh").
SERVICE wikibase:label { bd:serviceParam wikibase:language "zh" }
}
```
### when one of S or O is quantity (with unit)
e.g. 台北101有多高? 838公尺
### when one of S or O is string
e.g.  川普有几個子女? 5