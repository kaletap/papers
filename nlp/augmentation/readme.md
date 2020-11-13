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
* For each x_i in x_{\<t}, with probability \gamma replace x_i with a sample from the unigram frequence distibution
* blank noising: for each x_i in x_{\<t}, with probability \gamma replace x_i with a placeholder token "_"

It seems very easy, just insert random word (highly probable) to the sentence. The work seems mostly theoretical and aims to justify this approach. They also explore inserting words not from a unigram distribution but a little bit more complicated one. For example, we may want to not to noise words that are inside a common bigram (since that doesn't make a lot of sense). Or increase the probabilities of words that complete a large number of bigrams.

Some description of this (and related) methods is in a blogpost by Edward Ma (https://medium.com/towards-artificial-intelligence/how-does-data-noising-help-to-improve-your-nlp-model-480619f9fb10).

## Data Augmentation using Pre-trained Transformer Models
https://arxiv.org/abs/2003.02245

### Overview

Explores the idea of using pretrained transformer for data augmentation.

They use Bert (MLM), GPT-2 (autoregressive LM) and BART (seq2seq) for (conditional(?)) data augmentation. What is contitional data augmentation? It takes the label into account. For example CBERT (conditional Bert) considers class labels to predict the masked tokens. It relies on modifying BERT model segment embedding (what is it?).

It seems, that someone used GPT-2 for DA already (Anaby-Tavor et.al 2019).

This paper proposes a 'unified approach to use pre-trained transformer based models for data augmentation'. 

They said that they are releasing code, but they didn't (due to some legal issues in Amazon, idk).

seq2seq seems to be the best: nice. I'll have to try it. Maybe I can implement it as well. It's just Auto-Encoder, we want to output the same sentence, only with some small changes. There has been some work on seq2seq trained for denoising (of course, I shouldn't be surprised here): Lewis et al. 2019, Raffe et al. 2019. I had no idea that someone have tried it already. 

I'll have to read about this CBERT, seems interesting, no idea why I've missed that. 

But conditional language modeling can be done easier. For each sentence, we prepend [class] [sep]> tokens before the rest. Then we fine-tune language model. Should be very easy, we can compare it to:
1. model with no finetuning
2. model with standard fine-tuning

We can do the same for auto-regressive model like GPT.

In seq2seq (BART) experiments, they found out that subword-level masking is inferior to word-level (useful to note, since nlp-aug uses token-level augmentation). They apply masking to 20% of the words and train BART with denoising objective (the goal is to decode the original sequence given a masked sequence). What exactly is denoising objective? Do we simply want to decode the sequence, and mask some of decoder's tokens? 

It's worth observing how they do the sampling in GPT generation: top_k=0 and top_p=0.9. That means, that they take only 0.9 top probable words, making it impossiible to sample less likely words. That seems reasonable. This strategy is called 'nucleus sampling' (Holtzman et al. 2019).

### Experiments
They compare themselves to EDA baseline and CBERT ('the latest model-based augmenation that outperforms ther word replacement methods'). For datasets they use:
* SST-2
* SNIPS (7 intents from Snips personal voice assistant)
* TREC question classification dataset sourced from TREC

It seems that TREC is used in many experiments (ULMFit, EDA, this one). Maybe it is worth it to use it in my experiments as well. To simulate low-data regime setting they sample 1% of data. I don't know if that's the better than just taking a given sample size. They focus on 1% regime, since that's where seems to be the most to gain. BERT performs well even for 10% of training data (that agrees with my observations as well).

They repeat experiments 15 times to account for stochsticity. Besides measuring accuracy they look into aspects of the generated text. First one is how well the class label is preserved (which is simply measured by classifier accuracy). The second one is text diversity (type token ratio: number of unique n-grams divided by the number of all n-grams).

Their results are quite interesting. On this 1%, low-data regime setting results are quite bad. EDA makes performance worse. Bert augmentation usually improves performance (but not for TREC), GPT-2 (context) does a little bit worse than Bert. Additional conditional pretraining improves performance in all cases (especially for GPT-2, but it's not that easy to do meaningful, label-preserving training). As mentioned in the beginning, Baert has the best results and it's the only
method that improves results on TREC.

Bart is also a compromise between label preservation (which Bert does the best) and diverse content production (which GPT-2 does best). 

### Summary
(Conditionally) pretrained transformers can be useful for data augmentation when we do not have a lot of data. Methods can be combined with 'other advanced in text content manipulation such as co-training the data generator and classifier': Hu et al. 2019.

