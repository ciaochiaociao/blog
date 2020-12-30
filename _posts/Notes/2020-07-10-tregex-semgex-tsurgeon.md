---
title: Tregex/Semgex/Tsurgeon
---

https://nlp.stanford.edu/software/tregex.html
https://nlp.stanford.edu/nlp/javadoc/javanlp/edu/stanford/nlp/trees/tregex/tsurgeon/Tsurgeon.html
https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html

```
{word:Jones} [<< ({pos:/VB.*|ADJ.*/}=context1 >> {pos:/RB/}=context2) | </compound|flat.*/ {}=context1 ]
{} [>> {word:Jones} | << {word:Jones}]
{pos:/VB.*/}=verb >nsubj {lemma:jones} >/.*mod/ {}=adv
{lemma:/^(?!boy).*$/} => any word that isn’t “boy”
{lemma:/^.*(?<!boy)$/} => any word that isn’t “boy”

```

- Nodes are represented as {attr1:value1;attr2:value2;…}
- Grouping nodes: 
	- `[{} | {}]` e.g. [ {lemma:locate} | {ner:LOCATION} ] A node that is either a word with a lemma “locate” or a word with LOCATION ner
	- `( {} == !{} )` e.g.`( {word:Paris} == !{ner:LOCATION} )` A node that has the text Paris without being a Location


(tail) dependent <--- governor (head)
```
A <reln B : A is the dependent of a reln  relation with B
A >reln B : A is the governor of a reln relation with B
A <<reln B : There is some node in a dep<gov chain from A that is the dependent of a reln relation with B
A >>reln B : There is some node in a gov>dep chain from A that is the governor of a reln relation with B
A @ B : A is aligned to B through an Alignment object
```

 - Relation types can be regular strings or regular expressions encased by “/”

 - All relations are relative to first node in string
```
{} >nsubj {} >dobj {}
& symbol is optional: {} >nsubj {} & >dobj {}
```

 - Operators can be combined via “or” with `|`, and "and" with `&`

 - Nodes can be grouped w/ parentheses `()`
```
{pos:NN} @ ({} <nsubj {})
“A noun that is aligned to a node that is the dependent of an nsubj relation ”
Not the same as {pos:NN} @ {} <nsubj {}
```
 - Any relation can be negated with “!” prefix
 - Grouping relations: `[ ... | ... & ... ]` e.g.`[ <prep_in {} | <prep_on {}]`
 - Negate relation: `![...]`
 - Named nodes and named relation: `{}=node` `</.*subj/=rel` `<=rel`
- Optional relation prefix `?`: `?>...` `?[...]`


## java regex
```
(?<name>X)	X, as a named-capturing group
(?:X)	X, as a non-capturing group
(?=X)	X, via zero-width positive lookahead
(?!X)	X, via zero-width negative lookahead  <==
(?<=X)	X, via zero-width positive lookbehind
(?<!X)	X, via zero-width negative lookbehind <==
(?>X)	X, as an independent, non-capturing group
```