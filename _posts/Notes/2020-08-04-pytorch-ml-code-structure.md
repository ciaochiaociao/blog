---
title: Pytorch Machine Learnine Code Structure (Framework)
---

|                         | huggingface                                           | flair                                                        | Pytorch                                 |
| ----------------------- | ----------------------------------------------------- | ------------------------------------------------------------ | --------------------------------------- |
| tokenization            | in `Tokenizer` and `Dataset`                          | in `Dataset`                                                 |                                         |
| encoding (conver_to_id) | in `Tokenizer` and `Dataset`                          |                                                              |                                         |
| embedding               | in `tagger.forward()`                                 | in `Embeddings.embed(Sentence)` called in  `tagger.forward()` and `Token.get_each_embedding(Sentence)` |                                         |
| move to gpu             |                                                       | in `Token.get_each_embedding(Sentence)`                      |                                         |
| padding                 | in `Tokenizer` and `Dataset`(before `Dataloader`)     | in `tagger.forward()`                                        | in `DataLoader`'s argument `collate_fn` |
| batching                | in `DataLoader`                                       | in `DataLoader`                                              | in `DataLoader`                         |
| creating mask           | in `Tokenizer`and `Dataset`                           | in `Dataset`                                                 |                                         |
| batching mask           |                                                       |                                                              |                                         |
| masking                 |                                                       | in `tagger.forward()`                                        |                                         |
| `Dataset.__getitem__()` | generate `tensor`s, e.g., padded `input_ids`, `masks` | generate `Sentence`                                          |                                         |
| `tagger.forward()`      | `tensor` to  loss and `tensor` (logits)               | `Sentence`s to `tensor` (`features`)                         |                                         |
| loss                    | in `model.forward()`                                  | in `model.forward_loss()`                                    |                                         |
| others                  |                                                       | clear references to the embeddings in `store_embeddings` called at the end of each epoch |                                         |

## Flair train loop

```
for self.epoch in range(self.epoch + 1, max_epochs + 1):
	optimizer: torch.optim.Optimizer = self.optimizer(
            self.model.parameters(), lr=learning_rate, **kwargs
        )
    lr_scheduler = scheduler(
        optimizer,
        factor=anneal_factor,
        patience=patience,
        initial_extra_patience=initial_extra_patience,
        mode=anneal_mode,
        verbose=True,
        )
	# get new learning rate
    for group in optimizer.param_groups:
        learning_rate = group["lr"]

	if learning_rate < min_learning_rate:
        log.info("learning rate too small - quitting training!")
        break
	batch_loader = DataLoader(
                    train_data,
                    batch_size=mini_batch_size,
                    shuffle=shuffle,
                    num_workers=num_workers,
                    sampler=sampler,
                )
	self.model.train()
    for batch_no, batch in enumerate(batch_loader):

        # zero the gradients on the model and optimizer
        self.model.zero_grad()
        optimizer.zero_grad()

        # if necessary, make batch_steps
        batch_steps = [batch]
        if len(batch) > micro_batch_size:
            batch_steps = [
                batch[x : x + micro_batch_size]
                for x in range(0, len(batch), micro_batch_size)
            ]

        # forward and backward for batch
        for batch_step in batch_steps:

            # forward pass
            loss = self.model.forward_loss(batch_step)

            # Backward
            loss.backward()

        # do the optimizer step
        torch.nn.utils.clip_grad_norm_(self.model.parameters(), 5.0)
        optimizer.step()
        
        store_embeddings(batch, embeddings_storage_mode)
    self.model.eval()
```



## HuggingFace train loop

```
for epoch in train_iterator:
	epoch_iterator = tqdm(train_dataloader, desc="Iteration", disable=not self.is_local_process_zero())
	for step, inputs in enumerate(epoch_iterator):
		if (step + 1) % self.args.gradient_accumulation_steps == 0 or (
              # last step in epoch but step is always smaller than gradient_accumulation_steps
              len(epoch_iterator) <= self.args.gradient_accumulation_steps
              and (step + 1) == len(epoch_iterator)
         ):
         	outputs = model(**inputs)
         	loss = outputs[0]
         	if self.args.n_gpu > 1:
            	loss = loss.mean()
            if self.args.gradient_accumulation_steps > 1:
            	loss = loss / self.args.gradient_accumulation_steps
            loss.backward()
            tr_loss += loss.item()
         	torch.nn.utils.clip_grad_norm_(model.parameters(), self.args.max_grad_norm)
         	self.optimizer.step()
         	self.lr_scheduler.step()
         	self.evaluate()
```

| Tasks                         | Hugging Face v4.5.1                  | AllenNLP |
| ----------------------------- | ------------------------------------ | -------- |
| Single-Span Extraction        | `AutoModelForQuestionAnswering`      |          |
| Sequence Classification       | `AutoModelForSequenceClassification` |          |
| Token Sequence Classification | `AutoModelForTokenClassification`    |          |

