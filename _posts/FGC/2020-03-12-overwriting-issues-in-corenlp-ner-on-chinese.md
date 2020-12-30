---
title: Overwriting Issues In CoreNLP NER on Chinese
---

## English RegexNER DOES NOT overwrite the existing label
**Before Adding Regex Dictionary File**
```
> new york
LOCATION
> york
O
```

**After**
regex file
```
york	misc	O	1
```

```
> new york
LOCATION
> york
misc
```

## Chinese RegexNER OVERWRITES the existing label
**Before Adding Regex Dictionary File**
```
> 中华民国
ORGANIZATION 中华 民国
```

**After Adding**
```txt
#regex file
民国	LOCATION	O	1
```

```txt
#properties file
annotators = tokenize, ssplit, pos, lemma, ner

# segmenter
...

# ner
...

# regexner
ner.fine.regexner.mapping = ...
```

---

```
> 中华民国
ORGANIZATION 中华
LOCATION 民国
```