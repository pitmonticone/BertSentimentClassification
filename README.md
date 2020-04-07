# Data Mining Mini-Project: BERT 

## Team 
* [Monticone Pietro](https://github.com/pitmonticone)
* [Moroni Claudio](https://github.com/claudio20497)
* [Orsenigo Davide](https://github.com/dadorse)

# Data Mining Mini-Project: BERT

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

## Notebooks 
#### Submission
  * [BERT-base]()
  * [BERT-large]()

#### Attempts
  * [BERT-tpu](https://www.kaggle.com/claudiomoroni/berttpu)
  * [spaCy]()

## Data 
* [bert_final_data](https://www.kaggle.com/claudiomoroni/bert-final-data) 
* [IMBD_data](https://www.kaggle.com/davideorsenigo/imbddata)

## References
* [BERT: *Pre-training of Deep Bidirectional Transformers for Language Understanding*](https://arxiv.org/abs/1810.04805) 
* [A Visual Guide to Using BERT for the First Time](https://jalammar.github.io/a-visual-guide-to-using-bert-for-the-first-time/)
* [Machine Translation(Encoder-Decoder Model)!](https://medium.com/analytics-vidhya/machine-translation-encoder-decoder-model-7e4867377161)
* [The Illustarted Tranformers](https://jalammar.github.io/illustrated-transformer/)
* [The Illustrated BERT, ELMo, and co. (How NLP Cracked Transfer Learning)](https://jalammar.github.io/illustrated-bert/)
* [BERT Explained: State of the art language model for NLP](https://towardsdatascience.com/bert-explained-state-of-the-art-language-model-for-nlp-f8b21a9b6270)
