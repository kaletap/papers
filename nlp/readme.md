## Universal Language Model Fine-tuning, Howard & Ruder

"Language modeling captures sentiment: Radford 2017, Leaerning to generate reviews and discovering sentiment". I would like to see their justification for that, it is not clear for me that they really do.

"Formally, language modeling induces a hypothesis space H that should be useful for many other NLP tasks (Vapnik and Kotz, 1982)": I don't know what that means, look it up.

ULMFiT
1. Pretrain on Wikitext-103, 103 mln words (something like 500MB). As they say, pretraining enables generalization for tasks with 100 labeled examples. So it should also be true for transformer model as well.
2. Pretrain on task data. They use two tricks here: different learning rate for each layer (the highest for last layer), and use triangular learning rate schedule. 
3. Target classifier fine-tuning. This part consists of the most tricks. I have a lot of respect for the authors for making this approach work. Even though the idea of pretraining and fine-tuning language model is straightforward, it is sometimes difficult to assess why something does not work and whether improving the code will help.
3.1 concat pooling: for classifier, the input is concatenation of last hidden state, mean of hidden states, maxpool of hidden states of language model.
3.2 gradual unfreezing of LM layers. We don't fine tune everything at once (like we do with bert nowadays), but one layer at a time.
3.3 some engineering tricks to make it work with longer sentences (batching)
3.4 bidirectional language models: the output of a classifier it the average of predictions 

In experiments they focus on ONLY on classification (sentiment analysis, question classification, topic classification) since they have many practical applications. Six widely studied datasets:
* binary movie review (IMDB, Maas et. al. 2011) [sentiment analysis]
* binary Yelp review dataset [sentiment analysis]
* 5-class Yelp review dataset (Zhang et. al. 2015) [sentiment analysis]
* TREC dataset for question classification [question classificaton]
* large-scale AG news and DBpedia ontology datasets (Zhang et. al. 2015) [topic classification]

I should use these datasets as well.

It's very cool, that they provide a set of hyperparameters. It makes it easier to implement the solution and some authors don't do it and you have to go thorugh the code to figure it out.

Results: state of the art, improvement over for example flair.

Analysis: pretraining is very important (thorough ablation analysis).

Overall, good paper: idea, engineering implemetation, experiment setup, analysis of results. I should also remember the datasets they used to make my own experiments as well. 

