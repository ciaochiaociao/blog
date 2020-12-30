---
title: Train On Generated Dataset by `OpenNER`
---

## Model to be Corrected Information
- FLAIR: https://github.com/flairNLP/flair
- BERT-NER: https://github.com/ciaochiaociao/BERT-NER
	- forked from https://github.com/kamalkraj/BERT-NER


Error Recovery Datasets:
- 10 folds on train set, dev set, test set along with predictions
- FLAIR on CoNLL03
- FLAIR on BERT	 
- FLAIR on WNUT17

## Correction Model Information
- Model: `bert-base-cased`
- Used Package: https://github.com/zmd971202/OpenNER
	- implictly forked from https://github.com/kamalkraj/BERT-NER
- My forked version: https://github.com/ciaochiaociao/OpenNER
- batch size: 32
- epoch: 5 (paper) 3 (default in code)
- learning rate: 1e-5 (paper) 5e-5 (default in code)
- warmup: 40% (paper) 10% (default in code)
- embedding of NE labels: one hot (paper) / add trainable / concat trainable
- early stop using dev set f1 score with patience 5
- encode predicted NE as an embedding and concatenated after bert hidden state (zeros intialized)
- loss: cross-entropy loss
- optimizer: BertAdam (fp32) or FusedAdam (fp16)
- no weight decay in `bias` and `LayerNorm`
- Used Tag Scheme: `IOB2`
- Labels: O, B-LOC, I-LOC, B-PER, I-PER, B-ORG, I-ORG, B-MISC, I-MISC, `[CLS]`, `[SEP]` (ref: https://github.com/kamalkraj/BERT-NER/issues/21, https://github.com/kamalkraj/BERT-NER/issues/1, https://github.com/kamalkraj/BERT-NER/commit/ec7f643c679649bbd9e71ccde0528dd3e8ef5757, https://github.com/kamalkraj/BERT-NER/pull/5/commits/a9862a8d82bb224ebe532c5c9648c2df92213393)
- Decoding with post-processing invalid/unexpected `[SEP]` to `O`
- Used framework:
  ```
	torch==1.0.1.post2
	pytorch-pretrained-bert==0.6.1
	#metric
	seqeval==0.0.5
	```
	
- Notes
	- `[SEP]` might appear in the middle of the sentence
	- Padded tokens will still be tagged with random labels
	- subwords starting with `##` do not have labels/features and the attentions are applied `label mask` (`1` means `masked`)
	- words more than `max_seq_len` are truncated
  - `ptvsd` for remote debugging
  - distributed training (`torch.distributed.init_process_group(backend='nccl')`, ... )
  - gradient accumulation
- running command:
`make train_recovery_model output_dir=models/opennercorr_lr5e-5_b8_e5_wu0.4`
```
python3 run_correction_model.py --train_file=data/recovery/flair.conll.train.10_fold.pred.gold.iob2 --dev_file=data/recovery/flair.conll.testa.pred.gold.iob2 --test_file=data/recovery/flair.conll.testb.pred.gold.iob2 --bert_model=bert-base-cased  --task_name=ner --output_dir=models/opennercorr_lr5e-5_b8_e5_wu0.4 --max_seq_length=128 --do_train --train_batch_size 8 --num_train_epochs 5 --do_eval --warmup_proportion=0.4
```

## Results


|                  | batch size | learning rate | train (10 folds) f1 |    dev f1   |   test f1   |
|------------------|------------|---------------|---------------------|-------------|-------------|
|                  |          8 |          5e-5 | 94.28/99.53         | 99.98/94.40 | 93.03/90.09 |
| Released         |         32 |          1e-5 | 94.28/99.87         | 99.98/93.57 | 93.03/89.73 |
| Default in Code  |         32 |          5e-5 | 94.28/98.97         | 99.98/93.96 | 93.03/90.08 |
| + token majority (one-hot) |         32 |          5e-5 | 94.28/99.98         | 99.98/94.74 | 93.03/90.71 |
| + token majority (add embedding)|         32 |          5e-5 | 94.28/99.82        | 99.98/94.69 | 93.03/90.33|
| + token majority (concat embedding)|         32 |          5e-5 | 94.28/99.93        | 99.98/94.59 | 93.03/91.03|