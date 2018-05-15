# Abstract
  The purpose of this document is to introduce the reader to our current research and development state
for fake news detection ana anylisis.


# Introduction

We are on the opinion that the task of detecting fake news is complex even for humans. That is why we decided to approach
the problem not by automatically detecting whether an article is fake or not but to analize the content and provide
meaningful insights such as sentiment, category, summary and many others.


# Stance Detection

## Motivation
Our main motives towards implementing a stance detection solution is pretty well said in the introduction of the [http://www.fakenewschallenge.org/](http://www.fakenewschallenge.org/) competion
> In particular, a good stance detection solution would allow a human fact checker to enter a claim or headline and instantly retrieve the top articles that agree, disagree or discuss the claim/headline in question. 
> They could then look at the arguments for and against the claim, and use their human judgment and reasoning skills to assess the validity of the claim in question. 
>Such a tool would enable human fact checkers to be fast and effective.

Our goal is not only to output whether the headline agree, disagree or discuss the main content but to find those phrases
in the text that contribute most to the result.

## [Implementation](https://github.com/radpet/fake-news-detector/tree/master/stance)

The dataset we used for training was one from the [http://www.fakenewschallenge.org/](http://www.fakenewschallenge.org/) competion. We did a brief data exploration in order to obtain intuition about the data inside. [Link to data exploration notebook](https://github.com/radpet/fake-news-detector/blob/master/stance/Data%20Exploration.ipynb)

We splitted the provided train data into 3 parts - 80/10/10 Train, Dev (validation) and Test. There was a catch with the splitting we found the hard way. After splitting in random with stratify and training a model we received exceptionally good result on our test set. Later when tested on the provided test set the model score was alwful. This was caused by duplicating articles both in train and test split. Our hypothesis is that the network seem to have learning the relationship between the exact feature vectors rather than generalizing. We then split the train set again in 80/10/10 manner but there were no duplicate articles in the other splits. One can view the splitting script [here](https://github.com/radpet/fake-news-detector/blob/master/stance/split_train.py)

We tried two deep learning approaches. We both experimented with LSTM and GRU but 
* Tokenize the headline and use glove word embeddings. Encode headline with bidirectional reccurent network (GRU).
Tokenize the article and use glove word embeddings. Encode the article using bidirectional reccurent network(GRU). Concatanate the output vectors of the headline and article and run the extracted features through 2 dense layers.

This model did not have good score (*Should train again because I forgot what the scores were)

* The second approach adds attention layers after each of the bidirectional GRU and use the 'weighted' output vector for classification.

A model checkpoint can be found here [here](https://github.com/radpet/fake-news-detector/tree/master/stance/checkpoints/2018-05-13_16:54:37) and its source [here](https://github.com/radpet/fake-news-detector/blob/master/stance/bi_lstm_baseline.py)

Remaining tasks:
* Experiment adding more features derived from other models.
* Since we introduced attention mechanism we hope that using the learned weights we can visualize which phrases the network emphasize on.


