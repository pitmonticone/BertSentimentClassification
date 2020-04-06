# Data Mining Mini-Project: BERT 

## Team 
* [Monticone Pietro](https://github.com/pitmonticone)
* [Moroni Claudio](https://github.com/claudio20497)
* [Orsenigo Davide](https://github.com/dadorse)

## Theory
### Encoders/decoders sequences

Occorre anzitutto capire cos'è una sequenza di encoders / decoders, e a cosa serve. Farò riferimento a [Machine Translation Encoder Deocoder Model](https://medium.com/analytics-vidhya/machine-translation-encoder-decoder-model-7e4867377161) e a [Visualizing a Neural Machine Translation Model](https://jalammar.github.io/visualizing-neural-machine-translation-mechanics-of-seq2seq-models-with-attention/):<br>

In breve: l'applicazione di sequenze di encoders/decoders è principalmente la traduzione automatica di frasi da una lingua all'altra. Bisogna pensare alla sequenza encoders/decoders come una sequenza di passaggi (coders) che portano (per esempio) dalla frase inglese "the pen is on the table" alla frase italiana  "la penna è sul tavolo".<br>
In una sequenza encoders/decoders vengono prima tutti gli encoders e poi tutti i decoders, pertanto va visualizzata come:<br>

frase input $\to$ (encoders) $\to$ (decoders) $\to$ frase tradotta in output<br>

Per quello che ci riguarda, encoders e decoders sono la stessa cosa (a cui dunque ci riferiremo con la parola "coder"), e ciò che li distingue è il modo in cui ne trattiamo l'output.<br>
Un coder è costituito da due strati:<br>
- un "attention module" (AM) 
- una rete neurale LSTM o GRU.  

L'AM lo descriveremo successivamente, per ora lo trascuriamo e sarà dunque sufficiente identificare ciascun coder con la sua rete.<br>  
Non è importante capire nel dettaglio cosa voglia dire LSTM o GRU, l'unica cosa che va tenuta a mente è che sia l'input sia l'output di una LSTM/GRU è costituito da due vettori: il primo, che chiameremo "hidden state", rappresenta ciò che la rete ha imparato/capito *della frase che sta leggendo*, mentre il secondo, detto "prediction" è ciò che la rete prevede (ad es se la rete è fatta tradurre/classificare etc).<br>

Anzitutto, prima che la frase inglese incontri il primo coder della sequenza di encoders/decoders, ciascuna sua parola và tradotta nel relativo word embedding. Questo va fatto esternamente alla sequenza encoders/decoders, per esempio con Word2vec.<br>

Al primo coder della sequenza è passato il WEV della prima parola della frase e un hidden state random (ricorda che un coder è definito dalla sua rete, il cui input e output lo abbiamo descritto sopra). Dell'output,  la prediction è buttata via, mentre l'hidden state, assieme al WEV della seconda parola, sono passate in input al secondo coder e così via fino all'ultima parola della frase. <br>
In questa fase dunque si scartano le predictions e si tengono gli hidden vectors, e dunque i coders di questa fase cono detti "encoders".<br>
Terminata la successione di encoders ( che sono dunque in numero pari al numero delle parole della frase), l'ultimo hidden state prodotto è passato al primo coder della fase di decoding, assieme al WEV relativo al token di inizio frase "START". Il coder a questo punto produce un hidden state e una prediction. L'hidden state risultante, insieme con la prediction risultante che corrisponde a una sorta di WEV relativo alla prima parola della frase tradotta, vengono passati al secondo decoder ,che produrrà un nuovo hidden state e la seconda parola della frase tradotta, e così via finchè tutta la frase non è stata tradotta (la fine della traduzione è raggiunta quando uno dei decoders emette come prediction il vettore relativo al token "END"). Vi è poi un meccanismo esterno per convertire i vettori-predictions in vere parole.
*Quindi l'obiettivo dei decoders è solo quello di predire la prossima parola.*<br>


Questo è il funzionamento "ad alto livello" di una successione di encoders/decoders.<br>
Riguardo all'AM: prima abbiamo identificato un coder con una rete neurale. L'AM è un ulteriore strato che viene anteposto alla rete (quindi ora ogni coder è fatto di di uno strato di AM e da una rete, in quest'ordine), che si occupa di mettere in relazione le parole di una frase. Se, per esempio, ci concentriamo sulla parola "table" della frase da tradurre, il relativo encoder, grazie allo starto di AM, nel formulare la sua prediction e il suo hidden vector terrà maggiormente il considerazione la proposizione "on" (processata dall'encoder precedente) e di meno per esempio l'articolo "The" che è invece riferito a "cat". Vedere [Illustarted Tranformers](https://jalammar.github.io/illustrated-transformer/)  per maggiori info.<br>

### Bert

Definizione: un **Transformer** è un coder con anche lo strato AM. A volte si usa il termine **Tranformer** per indicare una sequenza di coders cona nche lo starto AM. Si è osservato cheq queste funzionano molto meglio delle sequenze encoders/decoders pure.

Definizione: **Bert** è un pre-trained Transformer Decoders stack. Dunque l'obiettivo con cui è stato allenato è quello di *predire la prossima parola* (e, vedremo, anche la prossima frase).
Facendo riferimento al link [illustrated Bert](https://jalammar.github.io/illustrated-bert/); In sostanza, come detto nella definizione di Transformer, questi si comportano meglio delle sequenze encoders/decoders pure. Tuttavia, nella sostituzione di queste coi Transoformers si perde una feature delle sequenze di encoders/decoders pure, ossia la *bidirezioanlità* (capacità di predire oltre alla parola successiva anche quella precedente). **Bert** è la soluzione a questo problema, ed è dunque un Tranformer che conserva la *bidirezionalità*.

Tecnicalità di Bert: per lui, il primo token non è "START", ma è "CLS". Nell'ottical di usare Bert come modello pre-trained per fare classificazione di frasi, questa è ottenuta dando come input a una regressione lineare la prediction output di Bert relativa al token "CLS". Questo è poichè Bert è stato anche allenato a *predire la prossima frase* ( e non solo dunque la prossima parola), e l'informazione relativa al senso della frase corrente viene codificata nel prediction output del token "CLS". Questo va visto come un document vector di 512 elementi.

Da qui in poi inizia la fase di [codice](https://jalammar.github.io/a-visual-guide-to-using-bert-for-the-first-time/)

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
