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


## Language Models Are Few-Shot Learners
I have hear about term "zero shot" before, but didn't really realize what it means. Once I've familarized myself with the details I was quite impressed with the idea (and that it works). However it does have it's shortcomings, it's interesting to read about them as well:
* https://lacker.io/ai/2020/07/06/giving-gpt-3-a-turing-test.html (very interesting and thorough examination of GPT-3 abilities)
* https://www.technologyreview.com/2020/08/22/1007539/gpt3-openai-language-generator-artificial-intelligence-ai-opinion another examination

My first impression was that is just a huge autoregressive model. The fact that it generates good samples is very impressive, but as authors admit, it does have limitations. Is there any interesting difference compared to GPT-2 model? Can this model be useful?


Their model does better on Machine Translation (unsupervised, since it was trained just on LM), than sota atchitectures, that's pretty crazy. "For both Fr-En and De-En,
few shot GPT-3 outperforms the best supervised result we could find but due to our unfamiliarity with the literature and
the appearance that these are un-competitive benchmarks we do not suspect those results represent true state of the art."

""We focused on exploring in-context learning behavior in autoregressive language models because it is straightforward to both
sample and compute likelihoods with this model class. As a result our experiments do not include any bidirectional
architectures or other training objectives such as denoising"

New research directions in NLP unsupervised learning:
"For all these reasons, scaling pure self-supervised prediction is likely to hit limits, and augmentation with a
different approach is likely to be necessary. Promising future directions in this vein might include learning the objective
function from humans [ZSW+19a], fine-tuning with reinforcement learning, or adding additional modalities such as
images to provide grounding and a better model of the world [CLY+19]."

## BART
In short: pretraining language models by msking spans of text and learning to predict them by using sequence to sequence (encoder-decode) model. It's good, because it allows for many input transformations, including changing the length of the input sentence. This approach (like Bert) can be seen as training denoising auto-encoder.

Transformations:
* token masking
* token deletion
* text infilling (masking spans of multiple words)
* sentence permutation
* document rotation

It can also be used for Machine Translation (with decoder producing English text). This is done be adding endoder layers from source language (like Romanian).

Overall, seems like and obvious thing to do and it's good that someone did it. Makes me wonder how many such things are obvious to do, but nobody did it yet (or not well enough). I guess that's why T5 was introduced concurrently by Google.

## Reformer: The Efficient Transformer
https://iclr.cc/virtual_2020/poster_rkgNKkHtvB.html

Likte the title says, it's a more efficient transformer, which achieves same results. It uses O(L logL) computations (runs faster) and takes less memory. Details are not that important (although very interesting), but I think that the main takeaway is just to use it instead of Bert/Roberta, especially for expeiriments involving fine-tuning a language model multiple times on long sequences.

