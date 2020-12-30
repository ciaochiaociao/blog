---
title: Out of Memory in HierAttn Model
---

```
batch_no: 83
			   try:
    264				for self.epoch in range(self.epoch + 1, max_epochs + 1):
				    	....
			   except KeyboardInterrupt:
					...
    573         # test best model if test data is present
    574         if self.corpus.test:
--> 575             final_score = self.final_test(base_path, mini_batch_chunk_size, num_workers)
    576         else:
    577             final_score = 0
    578             log.info("Test data not provided setting final score to 0")

trainer.py: ModelTrainer.final_test()
    604     def final_test(
    605         self, base_path: Union[Path, str], eval_mini_batch_size: int, num_workers: int = 8
    606     ):
    607         if type(base_path) is str:
    608             base_path = Path(base_path)
    609 
    610         log_line(log)
    611         log.info("Testing using best model ...")
    612 
    613         self.model.eval()
    614 
    615         if (base_path / "best-model.pt").exists():
    616             self.model = self.model.load(base_path / "best-model.pt")
    617 
    618         test_results, test_loss = self.model.evaluate(
    619             self.corpus.test,
    620             mini_batch_size=eval_mini_batch_size,
    621             num_workers=num_workers,
    622             out_path=base_path / "test.tsv",
--> 623             embedding_storage_mode="none",
    624         )

> sequence_tagger_model.py:SequenceTagger.evaluate()
    525         batch_no: int = 0
    526 
    527         lines: List[str] = []
    528 
    529         for batch in data_loader:
    530 
    531             # predict for batch
    532             loss = self.predict(batch,
    533                                 embedding_storage_mode=embedding_storage_mode,
    534                                 mini_batch_size=mini_batch_size,
    535                                 label_name='predicted',
--> 536                                 return_loss=True)
    537             eval_loss += loss
    538             batch_no += 1
    
   
> /home/cwhsutw1/.local/lib/python3.6/site-packages/flair/models/sequence_tagger_model.py(366)predict()
    364                     continue
    365 
--> 366                 feature = self.forward(batch)
> /home/cwhsutw1/.local/lib/python3.6/site-packages/flair/models/sequence_tagger_model.py(604)forward()
    602     def forward(self, sentences: List[Sentence]):
    603 
--> 604         self.embeddings.embed(sentences)

> /home/cwhsutw1/NER_Error_Recovery/globalner/models/nonlocal_encoder.py(86)forward()
     84 
     85         # calculate attention score
---> 86         _linear = self.linear(combined)  # (batch_size, awared_len, data_len, hidden_dim) / (batch_size, nonlocal_num, awared_len, data_len, hidden_dim)

> /opt/conda/lib/python3.6/site-
packages/torch/nn/functional.py(1676)linear()
   1674         ret = torch.addmm(bias, input, weight.t())
   1675     else:
-> 1676         output = input.matmul(weight.t())
   1677         if bias is not None:
   1678             output += bias
  
# nonlocals_batch.shape: torch.Size([8, 7, 74, 256])
# local_batch.shape: torch.Size([8, 35, 256])
# nonlocal_tok_attn_mask.shape: torch.Size([8, 7, 74])
# combined/input: torch.Size([8, 7, 35, 74, 512])
# weight: torch.Size([256, 512])
```