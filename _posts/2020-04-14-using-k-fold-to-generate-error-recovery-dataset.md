---
title: Using K-Fold To Generate Error Recovery Dataset
---

## Get Dev/Test Set
```
python3 -m globalner.data.eval_flair -m trained_models/pretrained_flair_on_conll/v0.4.pt -f data/processed/conll.testa.gold.iob1 --pred data/interim/conll.testa.gold.pred.conf.iobes --report data/processed/conll.testa.gold.pred.report
cut -d ' ' -f1,2,3 data/interim/conll.testa.gold.pred.conf.iobes > data/processed/conll.testa.gold.pred.iobes
python3 -m libs.ner_error_analysis.nlu.parser_utils -s bioes -t iob1 --cols 1 2 < data/processed/conll.testa.gold.pred.iobes > data/processed/conll.testa.gold.pred.iob1
python3 -m libs.ner_error_analysis.nlu.parser_utils -s bioes -t iob2 --cols 1 2 < data/processed/conll.testa.gold.pred.iobes > data/processed/conll.testa.gold.pred.iob2


python3 -m globalner.data.eval_flair -m trained_models/pretrained_flair_on_conll/v0.4.pt -f data/processed/conll.testb.gold.iob1 --pred data/interim/conll.testb.gold.pred.conf.iobes --report data/processed/conll.testb.gold.pred.report
cut -d ' ' -f1,2,3 data/interim/conll.testb.gold.pred.conf.iobes > data/processed/conll.testb.gold.pred.iobes
python3 -m libs.ner_error_analysis.nlu.parser_utils -s bioes -t iob1 --cols 1 2 < data/processed/conll.testb.gold.pred.iobes > data/processed/conll.testb.gold.pred.iob1
python3 -m libs.ner_error_analysis.nlu.parser_utils -s bioes -t iob2 --cols 1 2 < data/processed/conll.testb.gold.pred.iobes > data/processed/conll.testb.gold.pred.iob2
```

## Get Train Set by 10-Fold

`bash -x globalner/data/train_on_k_fold.sh`

```bash
set -e
DATA_DIR=data/processed/conll_train_10_folds
ROOT_EXP_DIR=exps/flair_on_conll_k_fold
for i in $(seq 0 9); do
  EXP_DIR=$ROOT_EXP_DIR/$i
  mkdir -p $EXP_DIR
  python3 -m globalner.data.train_flair \
    -d $DATA_DIR \
    --train $i.train.gold.iob1 \
    --dev None \
    --test $i.test.gold.iob1 \
    -o $EXP_DIR \
    --train-with-dev
#    --doc-sep=-DOCSTART- \
done || exit 1
```



### Evaluate On Kth Fold
```
python3 -m globalner.data.eval_flair -m exps/flair_on_conll_k_fold/2/final-model.pt -f data/processed/conll.testb.gold.iob1 --pred exps/flair_on_conll_k_fold/2/conll.testb.gold.pred.conf.iobes --report exps/flair_on_conll_k_fold/2/conll.testb.gold.pred.report
cut -d ' ' -f1,2,3 exps/flair_on_conll_k_fold/2/test.tsv | python3 libs/conlleval/conlleval_perl.py
cut -d ' ' -f1,2,3 exps/flair_on_conll_k_fold/2/test.tsv | python3 libs/conlleval/conlleval.py
```

### Get Error Recovery Dataset
```
bash globalner/data/get_recovery_data.sh exps/flair_on_conll_k_fold data/processed/conll.train.10_fold.gold.pred.iobes
python3 -m libs.ner_error_analysis.nlu.parser_utils -s bioes -t iob1 --cols 1 2 < data/processed/conll.train.10_fold.gold.pred.iobes > data/processed/conll.train.10_fold.gold.pred.iob1
python3 -m libs.ner_error_analysis.nlu.parser_utils -s bioes -t iob2 --cols 1 2 < data/processed/conll.train.10_fold.gold.pred.iobes > data/processed/conll.train.10_fold.gold.pred.iob2
```

### Evaluate On All K Folds
```
python3 libs/conlleval/conlleval_perl.py < data/processed/conll_n_fold.gold.pred.iobes
python3 libs/conlleval/conlleval_perl.py < data/processed/conll_n_fold.gold.pred.iob1
python3 libs/conlleval/conlleval_perl.py < data/processed/conll_n_fold.gold.pred.iob2
```