---
title: TorchText
---

 - ``torchtext.data.Dataset`: includes multiple `Example`s
   - `torchtext.data.TabularDataset`
 - `torchtext.data.Example`: normally not processed to tensor yet but just some texts
 - `torchtext.data.RawField`: Base class for all `Field`s
 - `torchtext.data.Field`: with instructions for converting to tensor
    - `preprocess()`
    - `postprocess()`
    - `process()`
       - calls `pad()` and  `numericalize()`
    - `numericalize()`
    - `pad()`
    - `build_vocab()`
    - `vocab.stoi`
    - `vocab.itos`
 - `torchtext.data.Batch`: `__init__()` calls `Field.process()` to convert to tensor
 - `torchtext.data.Iterator`: iter() calls `Batch.__init__()`
 - `torchtext.data.Pipeline`
 - `torchtext.get_tokenizer()`

```
SRC = Field(tokenize = "spacy",
            tokenizer_language="de",
            init_token = '<sos>',
            eos_token = '<eos>',
            lower = True)
TEXT = torchtext.data.Field(tokenize=get_tokenizer("basic_english"),
                            init_token='<sos>',
                            eos_token='<eos>',
                            lower=True)
```

