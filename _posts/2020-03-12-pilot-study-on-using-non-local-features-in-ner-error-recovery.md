---
title: Pilot Study on Using Non-Local Features In NER Error Recovery
---

## Reference
- Krishnan and Manning ACL 2006 “An Effective Two-Stage Model for Exploiting Non-Local Dependencies in Named Entity Recognition”
- Zhu et al. AAAI 2019 “Towards Open-Domain Named Entity Recognition via Neural Correction Models”

## Non-local features
From Krishnan 2006
1.token-majority embedding: 
> These refer to **the majority label assigned to the particular token** in the document/corpus. Eg: Suppose we have three occurrences of the token Australia, such that two are labeled Location and one is labeled Organization, our token majority feature would take value Location for all three occurrences of the token. This feature can enable us to capture some dependence between **token sequences corresponding to a single entity and having common tokens**.

```
(s1) ... [Australia]LOC ...
(s2) ... [... Australia]LOC ...
(s3) ... [Australia ...]ORG ...
> token-majority of (s1) "Australia": LOC
> token-majority of (s2) "Australia": LOC
> token-majority of (s3) "Australia": LOC
```

2.entity-majority embedding
> These refer to **the majority label assigned to the particular entity** in the document/corpus. 
> 
> Eg: Suppose we have three occurrences of the entity sequence (we define it as a token sequence labeled as a single entity by the first stage CRF) Bank of Australia, such that two are labeled Organization and one is labeled Location, our entity majority feature would take value Organization for all tokens in all three occurrences of the entity sequence. This feature enables us to capture the dependence between **identical entity sequences**. 

```
(s1) ... [Bank of Australia]ORG ...
(s2) ... [Bank of Australia]ORG ...
(s3) ... [Bank of Australia]LOC ...
> entity-majority of (s1) (s2) (s3) "Bank": ORG
> entity-majority of (s1) (s2) (s3) "of": ORG
> entity-majority of (s1) (s2) (s3) "Australia": ORG
```
> For token labeled as not a Named Entity by the first CRF, this feature returns the majority label assigned to that token when it occurs as a single token named entity.

```
(s1) ... [york]O ...
(s2) ... [york]PER ...
(s3) ... [york]PER ...
> entity-majority of (s1) "york": PER
```

3.superentity-majority embedding
> These refer to **the majority label assigned to supersequences of the particular entity** in the document/corpus. By entity supersequences, we refer to entity sequences, that strictly contain within their span, another entity sequence. 
> 
> For example, if we have two occurrences of Bank of Australia labeled Organization and one occurrence of Australia Cup labeled Miscellaneous, then for all occurrences of the entity Australia, the superentity-majority feature would take value Organization. This feature enables us to take into account **labels assigned to supersequences of a particular entity**, while labeling it. 

```
(s1) ...[Australia]LOC...
(s2) ...[Australia]ORG...
(s3) ... [Bank of Australia]ORG ...
(s4) ... [Bank of Australia]ORG ...
(s5) ... [Australia Cup]MISC ...
> superentity-majority of (s1) "Australia": ORG
> superentity-majority of (s2) "Australia": ORG
```

> For token labeled as not a Named Entity by the first CRF, this feature returns the majority label assigned to all entities containing the token within their span.

```
(s1) ... [york]O ...
(s2) ... [new york]LOC ...
(s3) ... [new york]LOC ...
(s4) ... [new york knicks]ORG ...
> superentity-majority of (s1) "york": LOC
```

## Pilot Study
 - NER Predictions: FLAIR on WNUT
 - Goal: See if the clustered redundancy/non-local information can help recover errors
 - Method: for every errors, check if the non-local information posseses some correct information
	 - the answer (benchmark) of the errors exist in other ner predictions (non-local)
 - Workspace: 227 conda base /d/ner_error_analysis/exps/recovery_non_local_features_pilot.ipynb
 - Data: FLAIR trained on CoNLL training set, predicting on CoNLL test set

Below shows a case that the non-local information is helpful for local information. As can be seen below, `D7-S2-NERErr1173` has a span error in failing to recognize the token `Wednesday` at the right of `Sheffield` as also a part of `ORG` entity mention. By retrieving the other tokens in other sentences that match the gold mention Sheffield Wednesday, `D205-S18`, `D207-S14`, `D208-S4` and `D210-S9` are obtained and all of them have the NER prediction as an `ORG`. A simple majority voting can be used to recover error  `D7-S2-NERErr1173`,

```
---Error---
[ID] D7-S2-NERErr1173
[predict] Sheffield (['ORG'])
[gold] Sheffield Wednesday (['ORG'])
[type] ['Right Diminished']
[sentence] Dutch forward Reggie Blinker had his indefinite suspension lifted by FIFA on Friday and was set to make his Sheffield Wednesday comeback against Liverpool on Saturday .

[non-local information]:
D205-S18 [Liverpool]ORG 0 [Sheffield Wednesday]ORG 1 ( [Whittingham]PER 22 ) .
D207-S14 [Sheffield Wednesday]ORG 16 6 6 4 17 18 24
D208-S4 [Liverpool]ORG suffered an upset first home league defeat of the season , beaten 1-0 by a [Guy Whittingham]PER goal for [Sheffield Wednesday]ORG .
D210-S9 [Liverpool]ORG 0 [Sheffield Wednesday]ORG 1
count_healthy_non_local: 4
count_toxic_non_local: 0
```

### FLAIR on CoNLL

Below shows a basic statistics of the non-local information of SOTA FLAIR model on CoNLL-03 dataset.

Entity Clustering By Exact Matching the Entity Words

 - NE Errors: 470
	 - has redundant NEs (exact match): 230 (49%)
		 - has at least one beneficial redundant NEs (beneficial: same NE Label as gold): 141 (30%)
		 - has no benefiical: 89 (19%)
 - avg. number of redundant NEs per NE error: 3.15
 - NE Corrects: 5268
	 - has redundant NEs (exact match): 3915 (74.32%)



```
len(parser.ner_errors): 470
	count_has_non_local: 230 (48.94%)
		recoverable (count_is_recoverable_by_majority): 70 (30.43%)
		not recoverable : 160 (69.57%)

		count_has_healthy_non_local: 141 (61.30%)
		count_has_no_healthy_non_local: 89 (38.70%)
	count_has_no_non_local: 240 (51.06%)

-----
count_non_local: 1481
avg. non-local per error (count_non_local/len(parser.ner_errors)): 3.15

len(parser.ner_corrects): 5268
	count_has_non_local: 3915 (74.32%)

		count_has_healthy_non_local: 3825 (97.70%)
		count_has_no_healthy_non_local: 90 (2.30%)
	count_has_no_non_local: 1353 (25.68%)

-----
count_non_local: 37153
avg. non-local per correct (count_non_local/len(parser.ner_corrects)): 7.05
```

### FLAIR on WNUT

||NE Errors|errors with  non-local|errors with healthy non-local|erros with major healthy non-local|
|---|---|---|---|----|
|FLAIR on WNUT|460|211(45.6%)|66 (66.8%)|91 (43.1%)|


```
#statistics
len(parser.ner_errors): 460
	count_has_non_local: 211 (45.87%)
		recoverable (count_is_recoverable_by_majority): 66 (31.28%)
		not recoverable : 145 (68.72%)

		count_has_healthy_non_local: 141 (66.82%)
		count_has_unhealthy_non_local: 70 (33.18%)
	count_has_no_non_local: 249 (54.13%)

-----
count_non_local: 1287
avg. non-local per error (count_non_local/len(parser.ner_errors)): 2.80
```

```
lefn(parser.ner_corrects): 5270
count_non_local: 37086
count_has_non_local: 3911
count_non_local/len(parser.ner_corrects): 7.03719165085389
count_has_non_local/len(parser.ner_corrects): 0.7421252371916508
count_has_major_healthy_non_local: 3711
count_has_major_healthy_non_local/count_has_non_local: 0.9488621835847609
toxic count (count_has_non_local-count_has_major_healthy_non_local): 200
(count_has_non_local-count_has_major_healthy_non_local)/len(parser.ner_corrects): 0.03795066413662239
```
As can be seen, out of 460 errors, 211 have non-local information, accouting for 45.9% of them. And 141 have at least one healthy non-local information, accounting for 66.8% of those who have non-local informaiton. That is, a maximum of 141 errors can be recovered ideally.


Moreover, 43% of those who have non-local information have major healthy non-local information, accounting for 19% of the errors. That is, a maximum of 91 errors can be recovered by using majority voting with non-local information.

For those errors with toxic non-local information, the errors will remain. 
For example, the below error has 4 healthy non-local information (`D205-S18`, `D207-S14`, `D208-S4`, `D210-S9`) and 15 toxic non-local information.
```
---Error---
[predict]  ([])
[gold] Wednesday (['ORG'])
[type] ['False Negative - ORG']
[sentence] Blinker missed his club 's last two games after FIFA slapped a worldwide ban on him for appearing to sign contracts for both [Wednesday]ORG and Udinese while he was playing for Feyenoord .
[ID] D7-S3-NERErr1174

non-local information:
**=> D7-S2 [Dutch]MISC forward [Reggie Blinker]PER had his indefinite suspension lifted by [FIFA]ORG on Friday and was set to make his [Sheffield]ORG Wednesday comeback against [Liverpool]ORG on Saturday .
D14-S4 [Lara]PER , disciplined for misconduct on Wednesday , was dismissed for five to extend a disappointing run of form on tour .
D19-S9 [Tour]MISC manager [Clive Lloyd]PER on Wednesday apologised for [Lara]PER 's behaviour in confronting [Australia]LOC coach [Geoff Marsh]PER in the opposition dressing room to protest against his dismissal in the second test on Tuesday .
D43-S12 [Weah]PER served out his suspension during [Milan]ORG 's 2-1 home defeat by [Rosenborg]ORG of [Norway]LOC on Wednesday .
D44-S6 The fans , in [Austria]LOC to watch their team play [Rapid Vienna]ORG last Wednesday , may have been involved in a pub brawl earlier , the spokeswoman said .
D92-S4 [Gaulieder]PER , formerly a member of Prime Minister [Vladimir Meciar]PER 's ruling [Movement for a Democratic Slovakia]ORG , was stripped of his parliamentary mandate on Wednesday after leaving the party last month in protest over what he said was a lack of democracy in the country .
D104-S11 On Tuesday and Wednesday , opposition leader [Aung San Suu Kyi]PER was restricted to her home by the military government to prevent her from being drawn into the protests .
D112-S11 [Mills]PER was scheduled to die Wednesday but had his sentence temporarily postponed by the [Florida Supreme Court]ORG .
D125-S8 [Boutros-Ghali]PER on Wednesday opened the door for other [Africans]MISC to contest his job by saying he was suspending temporarily his candidacy but was not withdrawing completely from the race .
D137-S2 The price of the [OPEC]ORG basket of seven crudes stood at $ 24.20 a barrel on Thursday , against $ 23.47 on Wednesday , the [OPECNA]ORG news agency said , quoting the [OPEC]ORG secretariat .
D153-S2 A [Reuter]ORG consensus survey sees medical equipment group [Radiometer]ORG reporting largely unchanged earnings when it publishes first half 19996/97 results next Wednesday .
=> D205-S18 [Liverpool]ORG 0 [Sheffield Wednesday]ORG 1 ( [Whittingham]PER 22 ) .
=> D207-S14 [Sheffield Wednesday]ORG 16 6 6 4 17 18 24
=> D208-S4 [Liverpool]ORG suffered an upset first home league defeat of the season , beaten 1-0 by a [Guy Whittingham]PER goal for [Sheffield Wednesday]ORG .
D209-S13 Postponed : [Airdrieonians]ORG v [Clydebank]ORG ( to Wednesday ) , East
=> D210-S9 [Liverpool]ORG 0 [Sheffield Wednesday]ORG 1
D217-S9 With 65 minutes gone [Indonesia]LOC 's [Widodo Putra]PER , who scored a spectacular goal against [Kuwait]LOC on Wednesday , was again on target , breaking through the [Korean]MISC defence to beat the keeper with a low shot .
D224-S2 [Vancouver Canucks]ORG star right wing [Pavel Bure]PER was suspended for one game by the [National Hockey League]ORG and fined $ 1,000 Friday for his hit on [Buffalo Sabres]ORG defenceman [Garry Galley]PER on Wednesday .
D224-S3 [Bure]PER received a double-minor penalty for high-sticking with 2:22 left in the first period of Wednesday 's 7-6 overtime win by [Vancouver]ORG after colliding with [Galley]PER in [Buffalo]LOC zone .
count_healthy_non_local: 4
count_toxic_non_local: 15
```


### Side-effects of Non-Local Information

Ideally, we can apply majority voting to every NER errors to try to recover them. However, the same recovering procedure will also apply to NER correct predictions, so the extra NER errors might be introduced, causing a **toxic recovery**.


However, it also shows that majority voting might introduce an extra of 200 errors. 


### Benefits of Multiple Passes of Error Recovery

In the example of the error `D7-S3-NERErr1174`, note that `D7-S2` sentence is also a non-local information, which is in the same document as the error `D7-S3-NERErr1174`. Although `D7-S2` non-local information is toxic, the corresponding error `D7-S2-NERErr1173` can be recovered by the 1st non-local NER pass and become healthy to `D7-S3-NERErr1174`. 

```
D7-S2 [Dutch]MISC forward [Reggie Blinker]PER had his indefinite suspension lifted by [FIFA]ORG on Friday and was set to make his [Sheffield]ORG Wednesday comeback against [Liverpool]ORG on Saturday .
```