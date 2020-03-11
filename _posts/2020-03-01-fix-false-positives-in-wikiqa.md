---
title: "(WikiQA Log) Fix False Positives in WikiQA"
---

## qids
```
# 国籍 ['D001Q01', 'D001Q03', 'D001Q06', 'D072Q03', 'D285Q01', 'D305Q06', 'D305Q08']
# 时间 ['D071Q12', 'D097Q04', 'D097Q05']
```

## v0.6
- TP Corrects:
['D001Q01', 'D001Q03', 'D001Q06', 'D072Q03', 'D285Q01', 'D305Q06', 'D305Q08']
- FP Errors:
['D001Q09', 'D013Q08', 'D071Q01', 'D071Q12', 'D071Q13', 'D077Q10', 'D097Q04', 'D097Q05', 'D247Q09', 'D254Q01', 'D275Q01', 'D293Q01', 'D302Q01', 'D305Q01']

### 年分
D071Q12, D097Q04, D097Q05
```
诺贝尔于哪一年过世?
诺贝尔出生于哪一年?
张大千哪一年去世?

```

To Do:
1. Add custom attributes in RegexParseQ: 出生年份、出生月份、...
2. Add custom processes in postprocess() for custom attributes
3. Turn-off the NE expansion for time datavalues


## v0.6~v0.7
- TP Corrects
['D001Q03', 'D001Q06', 'D004Q06', 'D072Q04', 'D247Q09', 'D274Q01', 'D275Q01', 'D285Q01', 'D293Q01', 'D305Q01', 'D305Q06', 'D305Q08']

- FP Errors
['D001Q09', 'D013Q08', 'D048Q02', 'D071Q01', 'D071Q12', 'D071Q13', 'D077Q10', 'D097Q04', 'D097Q05', 'D254Q01', 'D275Q03', 'D302Q01']

- New FP Errors
['D048Q02', 'D275Q03']
- Sacrificed
['D072Q03', 'D001Q01']
- Gained
['D275Q01', 'D247Q09', 'D305Q01', 'D293Q01', 'D274Q01', 'D004Q06', 'D072Q04']

```
# questions trying to answer: 816
# questions that activate WikiQA: 61 (7.5%) (Parse Q / Predicate Inference)
	# after entity linking: 61 (100.0%)
	# after traversing: 35 (57.4%)
	# after matching w/ passage: 24 (68.6%)
# questions answered: 24 (24 / 61 = 39.3%)
# corrects: 12
# errors: 12
Correct's QIDs: ['D001Q03', 'D001Q06', 'D004Q06', 'D072Q04', 'D247Q09', 'D274Q01', 'D275Q01', 'D285Q01', 'D293Q01', 'D305Q01', 'D305Q06', 'D305Q08']
[WARN] Sacrificed:  {'D072Q03', 'D001Q01'}
[INFO] Gained:  {'D275Q01', 'D247Q09', 'D305Q01', 'D293Q01', 'D274Q01', 'D004Q06', 'D072Q04'}
Error's QIDs: ['D001Q09', 'D013Q08', 'D048Q02', 'D071Q01', 'D071Q12', 'D071Q13', 'D077Q10', 'D097Q04', 'D097Q05', 'D254Q01', 'D275Q03', 'D302Q01']
Precision (excl. TN): 12 / 24 = 50.0%

```

## v0.7