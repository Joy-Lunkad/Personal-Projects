# Neural Machine Translation - English to Hindi

In this project, I try to solve Neural Machine Translation using Transformers and also using LSTMs+Attention. 

# Hindi English Corpora

I used the [Hindi English Corpora](https://lindat.mff.cuni.cz/repository/xmlui/handle/11858/00-097C-0000-0023-625F-0) to train and test the model on, which is a collection of around 120,000 English sentences with their Hindi translations. 

<img src="https://github.com/Joy-Lunkad/Personal-Projects/blob/947a85b6549fbbc037137d1f55d189953dfa3054/resources/Hindi2English_NMT/Untitled.png" width="800">

# Preprocessing

The preprocessing of the corpus is done in 4 steps:

1. Add a start and end token to each sentence. This is done so that the model can understand from where the sentence starts and where it ends. While generating a translation, the model can stop translating more tokens as soon as it encounters the 'END' token.
2. Clean the sentences by removing special characters since we want to have a limited vocabulary.
3. Create a word index and reverse word index (dictionaries mapping from word → id and id → word). This is done so that we can convert a sentence into its tokens and vice versa without searching the entire word space.
4. Pad each sentence to a maximum length so that we can pass the sentences in a batch to the model.

# Training Details

I used [tensorflow.keras.preprocessing.text.Tokenizer](https://www.tensorflow.org/api_docs/python/tf/keras/preprocessing/text/Tokenizer) and [tf.keras.preprocessing.sequence.pad_sequences](https://www.tensorflow.org/api_docs/python/tf/keras/preprocessing/sequence/pad_sequences) to Tokenize the data.

I, then generated the tokens, applied padding to them and made a [tf.data.Dataset](https://www.tensorflow.org/api_docs/python/tf/data/Dataset) pipeline.

## Architecture


<img src="https://github.com/Joy-Lunkad/Personal-Projects/blob/947a85b6549fbbc037137d1f55d189953dfa3054/resources/Hindi2English_NMT/Untitled%201.png" width="300">

I used a vanilla transformer architecture as described in [Attention Is All You Need](https://arxiv.org/abs/1706.03762). Transformer is solely based on attention mechanisms. Since it has less architecture design bias build into it than LSTMs or GRUs, given enough data, it outperforms them and currently sits on the top as the State Of The Art in Neural Machine Translation.

I also used Teacher Forcing while training significantly reduce the training duration. 

The model was trained for 15 epochs.

# Results

The transformer achieved 83 % training accuracy and 73% validation accuracy.

> The fact is that accurate translation requires background knowledge in order to resolve ambiguity and establish the content of the sentence.

To get the prediction out of a transformer model, we give the input sentence as input to the encoder, and we give the [start] token as input to the decoder.

Using these two inputs, the transformer then generates the next word in the translation. This next word the then along with the [start] token again given as input to the decoder and the input sentence is passed on the encoder. Using this, the transformer outputs the next token in the output sequence. We continue this till we encounter an [end] token from the transformer or till we reach max sequence length.

## Examples


<img src="https://github.com/Joy-Lunkad/Personal-Projects/blob/947a85b6549fbbc037137d1f55d189953dfa3054/resources/Hindi2English_NMT/Untitled%202.png" width="800">
