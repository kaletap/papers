# Augmentation in NLP tasks.

How to do text augmentation?

https://amitness.com/2020/05/data-augmentation-for-nlp/

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

## Conditional Bert Contextual Augmentation
https://arxiv.org/abs/1812.06705

Bert fine-tuned on task data with label infromation to improve word substitution augmentaiton. It works better than just Bert, since label infromation is taken into account. It outperforms other methods on all 6 datasets. However, only LSTMs and CNNs are evaluated. This is suprising, since authors describe the effectiveness of Bert for downstream tasks. Bert would most likely diminish the performance of their method (especially since they didn't decrease dataset size for their experiments).

References Kobayashi, 2018, who did similar thing but with LSTMs (https://www.aclweb.org/anthology/N18-2072/)

## Not Enough Data? Deep Learning to the Rescue!
### Text augmentation with GPT-2
https://arxiv.org/abs/1911.03118

They call their solution 'LAMBADA'. It's basically fine-tuning GPT on small data. They say it's better than other sota methods (which includes C-Bert, which they cite). Is it possible to fine-tune GPT just on 100 examples? What about 10? They say that this approach is 'counter-intuitive'. Maybe it is, since we are using language model for both sentence generation and classification.

They cite Kingma and Welling as well! Are they going to use VAEs?

Basically, there are 4 steps:
1. train a baseline classifier
2. fine-tune language model
3. generate new labeled sentences from scratch
4. filter out shitty ones (using classifier trained with 1.)

Sounds simple, I wonder how important this filtering is. Especially since the classifier can be quite bad if there is not enough samples. They use [y, SEP, x, EOS] format for their data. I wonder how important this <sep> is, since this approach didn't work too well in my experiments (label was hardly ever preserved). Authors report that it generates samples quite well. Is it maybe because GPT was trained on the same datasets they use for experiments? Could be the case I guess, would be interesting to test it on some private ones.

They generate 10x the number of the sentences and retain top sentences according to confidence of a classifier. 

They test their model on three classifiers: Bert, LSTM and SVM (why SVM for god's sake)? They compare their augmentation methods with EDA, CBert and CVAE (wait, what is CVAE?) on 3 datasets: ATIS, TREC and WVA. It seems that TREC is used a lot in data augmentation experiments or as a classification benchmark in general. Their task is to classify sentence, not an entire document! This can make things easier (and faster) to train.

Models:
* SVM
* LSTM
* Bert

Datasets:
* ATIS
* TREC
* WVA

Augmenters:
* EDA
* C-Bert
* C-VAE

They use datasets of sizes 5, 10, 20, 50 or 100 samples per class for training (these samples are the same for every experiment of course). It doesn't seem like a lot. Also, the differences in sizes look quite small.

They compare themselves to CVAE (Kingma and Welling, 2014). How did they train it? WIth standard RNN-based encoder and decoder, which kind of sucks. Was there any attemtp to leverage transformers for building VAE? Not to my knowldege, but maybe. I don't know yet how that would be done exactly for the decoder part, I guess one would have to get rid of decoder-encoder attenton and focus on only a single representation of encoder (how to construct it?).

Overall, I think that it incredibly sucks, that authors do not show almost any examples from their augmenters. I have no idea how good they are. I am pretty sure that CVAE based on RNNs is terrible (especially that it's based on idea from 2014).

Results: LAMBADA outperfrom all other methods (including CBERT).

Question: a lot of authors use fine-tuned transformer for augmentation, but not for classification. Is an improvement still visible when using fine-tuned language model?

They also mention theoretical possibility of generating samples for unseen classes (zero-shot learning, Socher et al. 2013 - seems that Socher published a lot of influential work). Doesn't seem likely that it is going to work, but interesting idea. But I know now why they use [SEP] token after class label. Class label is just a word of a class, not a special token. Thus it makes sense to use special token to determine, that we now generate new sentence.

They mention **self-supervised** described in Ruder and Plank 2018. Haven't heard of that. It seems that what we can do, is to simply label examples that are unlabeled with classifier that we already have. It is possibly that this would bootstrap the performance and it indeed does. But still LAMBADA is better than taking samples from a classifier. It would be kind of weird if a classifier with accuracy p had better accuracy after training on examples labeled by itself also with accuracy p. I need some more theory on this semi-supervised learning.

Lastly, they warn against data drifting, when data is dominated by generated, not true, samples. 

Overall, interesting approach, proabably worth examining. What's lacking is a list of examples of generated sentences chosen for each augmenter and each dataset. What sort of samples are accepted and rejected by classifier? 

Also, it's a shame that they do not test classifier fine-tuned on language modeling task as well (they use fine-tuning only for augmetnation language modeling).

General idea: maybe it would be worth it, to incorporate smaller weights in a loss for samples synthetically generated?

