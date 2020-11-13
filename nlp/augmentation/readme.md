# Augmentation in NLP tasks.

## EDA: Easy Data Augmentation Techniques for Boosting Performance on Text Classification Tasks
https://arxiv.org/abs/1901.11196

The idea is to test simple data augmentation techniques in NLP. They include:
* synonym replacement
* random insertion
* random swap
* random deletion

These techniques are very fast and can be performed on-the-fly. Authors test these on 5 classification datasets:
* SST-2 (Stanford Sentiment Treebank)
* CR - customer reviews
* SUBJ - subjectivity/objectivity dataset
* TREC: question type dataset
* PC Pro-Con dataset

They use varying dataset sizes: 500, 2000, 5000 and full.

They use neural models like CNN and RNN, but no transformers (even though work was published in 2019). They report some gains in performance (78.6-80.7 with training size 500, 85.6 to 86.4 with training size 200, 87.7 to 88.3 with training size 5000, 88.3 to 88.8 on full set).

They also do visualization of latent space embeddings (outputs of last dense layer) with t-SNE (it looks quite cool, it seems that augmented sentences are quite close). 

They examine, how each of four augmentations does individually compared to them combined.

Lastly, they check how much augmentation to use (how many sentences to add per sentence to the training dataset). It seems that for smaller datasets it is helpful to use a lot (sixteen) and it's probably due to less overfitting. For 2000 eight, for 500 and more 4.

They cite related work, some of it is much more complicated (like Hu's "Toward Controlled Generation of Text"), some use Language Models (LSTM), some backtranslation. 

"With the rate NLP reserach has progressed in recent years, we suspect that researchers will soon find higher-performing augmentation techniques that will also be easy to use."

They also note clearly, that EDA's improvement is negligible when using ULMFit (finetuning model pretrained on language modeling).

It is also worth noting, that a lot of papers use different benchmarks so it's difficult to compare results.

## Data Noising as Smoothing in Neural Network Language Models
https://arxiv.org/abs/1703.02573

I do not get this paper.

Data noising can be seen as a form of data augmentation (for rnn-based language models?).

What is noising? 
* For each x_i in x_{<t}, with probability \gamma replace x_i with a sample from the unigram frequence distibution
* blank noising: for each x_i in x_{<t}, with probability \gamma replace x_i with a placeholder token "_"

It seems very easy, just insert random word (highly probable) to the sentence. The work seems mostly theoretical and aims to justify this approach. They also explore inserting words not from a unigram distribution but a little bit more complicated one. For example, we may want to not to noise words that are inside a common bigram (since that doesn't make a lot of sense). Or increase the probabilities of words that complete a large number of bigrams.

Some description of this (and related) methods is in a blogpost by Edward Ma (https://medium.com/towards-artificial-intelligence/how-does-data-noising-help-to-improve-your-nlp-model-480619f9fb10).

## Data Augmentation using Pre-trained Transformer Models
https://arxiv.org/abs/2003.02245

Using Bert, GPT-2 and BART for (conditional(?)) data augmentation.

