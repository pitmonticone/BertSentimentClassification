<!-- Meta-Badges -->
</p>

<p align="center">
    <img alt="Size" src="https://img.shields.io/github/repo-size/pitmonticone/BertSentimentClassification">
  </a>
  <img alt="Forks" src="https://img.shields.io/github/forks/pitmonticone/BertSentimentClassification">
  </a>
  <img alt="Stars" src="https://img.shields.io/github/stars/pitmonticone/BertSentimentClassification">
  </a>
  <img alt="Languages" src="https://img.shields.io/github/languages/count/pitmonticone/BertSentimentClassification">
  </a>
  <a href="https://github.com/pitmonticone/BertSentimentClassification/graphs/contributors">
    <img alt="Contributors" src="https://img.shields.io/github/contributors/pitmonticone/BertSentimentClassification">
  </a>
  <img alt="MIT Licence" src="https://img.shields.io/github/license/pitmonticone/BertSentimentClassification">
  </a>
  <img alt="Twitter" src="https://img.shields.io/twitter/url?url=https%3A%2F%2Fgithub.com%2Fpitmonticone%2FBertSentimentClassification"
  </a>
  
</p>

<!-- Title -->
<h1 align="center">
  BERT Sentiment Classification
</h1>

<!-- Subtitle -->
<h3 align="center">
  An Application to the Large Movie Review Dataset
</h3>

<!-- Badges -->
</p>

<p align="center">
  <a href="https://www.kaggle.com/inphyt2020/dataminingproject">
    <img alt="Kaggle" src="https://kaggle.com/static/images/open-in-kaggle.svg">
  </a>
  <a href="https://nbviewer.jupyter.org/github/pitmonticone/BertSentimentClassification">
    <img alt="nbviewer" src="https://github.com/jupyter/design/blob/master/logos/Badges/nbviewer_badge.svg">
  </a>
  <a href="https://colab.research.google.com/github/pitmonticone/BertSentimentClassification/blob/master">
    <img alt="Colab" src="https://colab.research.google.com/assets/colab-badge.svg">
  </a>
  
</p>

## Team 
* Monticone Pietro | [Mail](pietro.monticone@edu.unito.it) | [Github](https://github.com/pitmonticone)
* Moroni Claudio | [Mail](claudio.moroni@edu.unito.it) | [Github](https://github.com/claudio20497)
* Orsenigo Davide | [Mail](davide.orsenigo@edu.unito.it) | [Github](https://github.com/dadorse) 

## How to Explore this Work

* Read the code in the [Jupyter notebooks](https://nbviewer.jupyter.org/github/pitmonticone/BertSentimentClassification/blob/master/Notebooks).
* Run the code in the [Kaggle notebook](https://www.kaggle.com/inphyt2020/dataminingproject).

## Problem: *Sentiment Classification*

A sentiment classification problem consists, roughly speaking, in detecting a piece of text and predicting if the author likes or dislikes what he/she is talking about: the input *X* is a piece of text and the output *Y* is the sentiment we want to predict, such as the rating of a movie review.

If we can train a model to map *X* to *Y* based on a labelled dataset then it can be used to predict sentiment of a reviewer after watching a movie.


## Data: *Large Movie Review Dataset v1.0*

The [dataset](https://ai.stanford.edu/~amaas/data/sentiment/aclImdb_v1.tar.gz) contains movie reviews along with their associated binary sentiment polarity labels. 

* The core dataset contains 50,000 reviews split evenly into 25k train and 25k test sets. 
* The overall distribution of labels is balanced (25k pos and 25k neg). 
* 50,000 unlabeled documents for unsupervised learning are included, but they won't be used. 
* The train and test sets contain a disjoint set of movies, so no significant performance is obtained by memorizing movie-unique terms and their associated with observed labels.  
* In the labeled train/test sets, a negative review has a score ≤ 4 out of 10, and a positive review has a score ≥ 7 out of 10. Thus reviews with more neutral ratings are not included in the train/test sets. 
* In the unsupervised set, reviews of any rating are included and there are an even number of reviews > 5 and ≤ 5.

#### Reference 
Andrew L. Maas, Raymond E. Daly, Peter T. Pham, Dan Huang, Andrew Y. Ng, and Christopher Potts. (2011). [Learning Word Vectors for Sentiment Analysis](https://ai.stanford.edu/~amaas/papers/wvSent_acl2011.pdf). The 49th Annual Meeting of the Association for Computational Linguistics (ACL 2011).

## Theory

### The encoder-decoder sequence

Roughly speaking, an encoder-decoder sequence is an ordered collection of steps (*coders*) designed to automatically translate  sentences from a language to another (e.g. the English "the pen is on the table" into the Italian "la penna è sul tavolo"), which could be useful to visualize as follows: **input sentence** → (*encoders*) → (*decoders*) → **output/translated sentence**. <br>

For our practical purpose, encoders and decoders are effectively indistinguishable (that's why we will call them *coders*): both are composed of two layers: a **LSTM or GRU neural network** and an **attention module (AM)**. They only differ in the way in which their output is processed. 

#### LSTM or GRU neural network
Both the input and the output of an LSTM/GRU neural network consists of two vectors: 
1. the **hidden state**: the representation of what the network has learnt about the sentence it's reading;
2. the **prediction**: the representation of what the network predicts (e.g. translation). 

Each word in the English input sentence is translated into its word embedding vector (WEV) before being processed by the first coder (e.g. with `word2vec`). 
The WEV of the first word of the sentence and a random hidden state are processed by the first coder of the sequence. Regarding the output: the prediction is ignored, while the hidden state and the WEV of the second word are passed as input into the second coder and so on to the last word of the sentence. Therefore in this phase the coders work as *encoders*.

At the end of the sequence of N encoders (N being the number of words in the input sentence), the **decoding phase** begins: 
1. the last hidden state and the WEV of the "START" token are passed to the first *decoder*;
2. the decoder outputs a hidden state and a prection; 
3. the hidden state and the prediction are passed to the second decoder; 
4. the second decoder outputs a new hidden state and the second word of the translated/output sentence

and so on up until the whole sentence has been translated, namely when a decoder of the sequence outputs the WEV of the "END" token. Then there is an external mechanism to convert prediction vectors into real words, so it's very importance to notice that **the only purpose of decoders is to predict the next word**.  

#### Attention module (AM)

The attention module is a further layer that is placed before the network which provides the collection of words of the sentence with a relational structure. Let's consider the word "table" in the sentence used as an exampe above. Because of the AM, the encoder will weight the preposition "on" (processed by the previous encoder) more than the article "the" which refers to the subject "cat". 

### Bidirectional Encoder Representations from Transformers (BERT)

#### Transformer
The transformer is a coder endowed with the AM layer. Transformers have been observed to work much better than the basic encoder-decoder sequences.

#### BERT

BERT is a sequence of encoder-type transformers which was pre-trained to *predict* a word or sentence (i.e. used as decoder). The benefit of improved performance of Transformers comes at a cost: the loss of *bidirectionality*, which is the ability to predict both next word and the previous one. BERT is the solution to this problem, a Tranformer which preserves *biderectionality*.

##### Notes  
The first token is not "START". In order to use BERT as a pre-trained language model for sentence-classification, we need to input the BERT prediction of "CLS" into a linear regression because 
* the model has been trained to predict the next sentence, not just the next word; 
* the semantic information of the sentence is encoded in the prediction output of "CLS" as a document vector of 512 elements.

![](https://github.com/pitmonticone/BertSentimentClassification/blob/master/Images/diagram.png)

## References

### Datasets

* [bert_final_data](https://www.kaggle.com/dataset/c57aa27bcd81c6062bf454a37f1c55a8730a90a69a9e95a49252fbd660befebf)
* Processed IMBD
  * https://www.kaggle.com/dataset/5f1193b4685a6e3aa8b72fa3fdc427d18c3568c66734d60cf8f79f2607551a38
  * https://www.kaggle.com/dataset/9850d2e4b7d095e2b723457263fbef547437b159e3eb7ed6dc2e88c7869fca0b
* [Bert-For-Tf2](https://www.kaggle.com/juanumusic/bert-for-tf2)

### Articles
* [Google github repository](https://github.com/google-research/bert)
* [BERT: *Pre-training of Deep Bidirectional Transformers for Language Understanding*](https://arxiv.org/abs/1810.04805) 
* [A Visual Guide to Using BERT for the First Time](https://jalammar.github.io/a-visual-guide-to-using-bert-for-the-first-time/)
* [Machine Translation(Encoder-Decoder Model)!](https://medium.com/analytics-vidhya/machine-translation-encoder-decoder-model-7e4867377161)
* [The Illustarted Tranformers](https://jalammar.github.io/illustrated-transformer/)
* [The Illustrated BERT, ELMo, and co. (How NLP Cracked Transfer Learning)](https://jalammar.github.io/illustrated-bert/)
* [BERT Explained: State of the art language model for NLP](https://towardsdatascience.com/bert-explained-state-of-the-art-language-model-for-nlp-f8b21a9b6270)
* [Learning Word Vectors for Sentiment Analysis](https://ai.stanford.edu/~amaas/papers/wvSent_acl2011.pdf). 
