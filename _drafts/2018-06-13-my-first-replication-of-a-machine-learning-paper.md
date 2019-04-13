---
id: 5
title: 'My First Replication of a Machine Learning Paper'
date: 2018-06-13T22:31:02+00:00
author: Thomas Lisankie
layout: post
guid: http://tomlisankie.com/blog/?p=5
permalink: /2018/06/13/first-post-and-why-ive-never-seriously-blogged-before/
categories:
  - Uncategorized
tags:
  - machine learning
---
I recently decided to replicate a really interesting paper I read on identifying metaphors in text with word embeddings and visual features. The paper is called [_Black Holes and White Rabbits: Metaphor Identification with Visual Features_](http://www.aclweb.org/anthology/N16-1020) and it is the first paper that uses word and phrase embeddings for metaphor identification as well as the first paper that integrates representations found from both linguistic and visual data. I've been extremely interested in machine learning techniques and others for identifying conceptual metaphors and other for a couple of years now. Not only is it ridiculously interesting, but I think it's one of the most important areas to be worked on in NLP for drastically decreasing the friction involved in interacting with natural language systems. Given the prevalence of analogy, conceptual metaphor, and other forms of domain transfer in our everyday language, progress on this problem would lead to drastic improvement in natural language processing systems and possibly even the beginning of true natural language _understanding_ systems. Because of all these reasons, I saw replicating this paper as a worthwhile effort in order to get a feel for the amount of work that went into it, gain further experience with building NLP systems, and have my own copy of the work to use in my own projects.

Paper Overview
==============

Introduction
------------

The paper begins by speaking about why metaphor processing is important to advancing NLP systems and what conceptual metaphor is. They differentiate their approach from the others by being the first approach to use both linguistic and visual representations for metaphor identification as well as being the first to apply word and phrase embeddings to metaphor identification.

Related Work
------------

I decided to not summarize the Related Work section since it will likely lead to tangents away from this paper.

Method
------

### Learning Linguistic Representations

The authors decided to use the skip-gram version of the Word2Vec model with negative-sampling. (If you know how Wod2Vec works just skip to "The Rest of the Authors' Method")

#### How Word2Vec Works

Word2Vec is a method for creating word embeddings where a neural network with one hidden layer is trained on a "fake" task. The task essentially goes like this:

1.  A word window is created with a center (the "input word") and words surrounding the center (the number of surrounding words taken into account can be specified with the "window size" hyperparameter). The center is initially the first word of the text.
2.  Ordered pairs are then formed with the first entry of each pair being the center word and the second entry being one of the other words in the window. Each of these ordered word pairs is used as a training sample for the network (the first entry is used as the input word and the second entry is used as the output word).
3.  Each of the words is represented as a one-hot vector (a vector with _n_-dimensions where _n_ is the size of our vocabulary and all entries are zero except for the word we're representing which is a 1). Training the model on these samples leads to it learning how likely each output word is to appear close to the input word.
4.  The window is then shifted one step to the right and steps 1-3 are repeated until all possible words pairs have been found on the text and used on the model for training.

The model itself consists of three fully-connected layers. The first is the input layer where the one-hot vector of the input word is fed into. The second layer is the only hidden layer of the model and consists of _e_ units where _e_ is the size of the embedding space you want to use (this size is a hyperparameter you get to tune and essentially represents the number of distinct semantic features you would like your model to represent). Note that the second layer has no activation function. The third layer is the output layer. The output of the model is an _n_-dimensional vector where each entry is the probability (determined by a softmax activation function) of each word being "near" the input word. Now here's where some of the real magic comes in with Word2Vec: once training on this task is complete, we simply throw away the output layer (hence why the task is considered "fake") so that the output of the network will be the weights of the hidden layer. This new _e_-dimensional output layer is a vector that "points" to where the word is in the embedding space and is closer to similar words and further from dissimilar words along the appropriate dimensions. Isn't that just so amazing?!

#### The Rest of the Authors' Method

The authors decided to use a dump of Wikipedia as their corpus (specifically a dump from August 2015). They lemmatized, tagged, and parsed the corpus using Stanford CoreNLP. There were two sets of embeddings (each with 100 dimensions) found using two separate passes over the corpus. The first pass was for finding word embeddings and the second for finding phrase embeddings. In this second pass, the vectorized forms of all of the contexts from the first pass remained the same. Since only verb-noun and adjective-noun phrases were used for finding metaphoricity, all phrases that did not fit this form were filtered out. All of these embeddings were trained on the corpus for 3 epochs with a context window of 5, and 10 negative-samples for every word-context pair.

**NLP Definitions** **Lemmatization** \- The process of grouping together all inflected forms of a word so that they are processed as a single kind.
**Tagging** \- (aka part of speech tagging) A process where all words in a corpus are marked with the part of speech they correspond to.
**Parsing** \- Representing the grammatical structure of sentences.

### Learning Visual Representations

The authors used [Caffe](http://caffe.berkeleyvision.org/) to build a convolutional neural network which they trained and then extracted image embeddings from. Their network had 5 convolutional layers followed by two fully-connected layers and finally a softmax layer for classification. They used a multinomial logistic regression as their objective function. They obtained their image embeddings in a similar way to how word embeddings are obtained in Word2Vec, namely cutting off the last layer after the model has been trained and using the output from the formerly second to last layer. In this case that penultimate layer was outputting a vector for a 4096-dimensional embedding space (which is just huge). These visual embeddings were created by using 10 images for every word and phrase obtained from the corpus in the "Learning Linguistic Representations" section. These images were obtained via Google Images. The final visual embeddings for each class were found by taking the average of each of the embedding vectors given for each sample fed into the model.

### Multimodal Fusion Strategies

In this section the authors describe different approaches to fusing representations from distinct modes of input and discuss the strategies they chose to employ. Multimodal fusion is necessary since they are trying to get results on how combining multiple modes of representation can affect learning of metaphoricity. The best approach (to avoid problems with data sparsity and poor performance) is usually to independently learn uni-modal representations (as described in the previous two sections) and then combine (i.e. "fuse" them) later in the process. After this fusing process the representations are now multimodal. Previous work (namely [Bruni et al., 2014](https://www.jair.org/media/4135/live-4135-7609-jair.pdf)) discussed different ways of combining (fusing) linguistic and perceptual modes. There are three different kinds of fusing:

1.  1.  Early fusion: Directly combines the representations from all the different modes first. No similarity score is (directly) computed and the joint representation is fed to the model as-is.
    2.  Middle fusion: Combines the representations from the different modes and then calculates similarity scores (these scores are relevant because metaphor by definition is dealing with the similarity of representations between two things).
    3.  Late fusion: Similarity scores are computed independently for each mode and then combined.

The authors exclusively experimented with middle and late fusion. When using middle fusion, they [L2 normalized](http://www.chioka.in/differences-between-the-l1-norm-and-the-l2-norm-least-absolute-deviations-and-least-squares/) and concatenated the vectors for linguistic and visual representations and computed a metaphoricity score based on this joint representation. With late fusion, they computed the metaphoricity scores independently for each mode and then combined the scores by taking their average.

### Measuring Metaphoricity

The goal of this section is to investigate different operations on the embedding vectors for figuring out whether or not two words in a phrase belong to the same or similar domains. The first set of experiments was done on the word-level embeddings acquired from the first pass over the corpus. These experiments used the [cosine similarity metric](https://en.wikipedia.org/wiki/Cosine_similarity) to determine whether or not two words belonged to the same domain. If the cosine similarity was high, the words were very likely from the same domain. If it was low, they were likely not from the same domain whatsoever. This metric was referred to as WordCos for the rest of the paper. The second set of experiments was done on the phrase-level embeddings acquired from the second pass over the corpus. The embeddings learned for the entire phrase are compared with the embeddings of each of the words that make the phrase up. This serves as a way to see whether or not the two words in the phrase are in similar domains or not. If they're not, the similarity between one word and the entire phrase will be low. There are three different pairs of vectors that are used with the cosine similarity metric. Here are each of the pairs:

1.  PhrasCos1: _phrase_ \- _word1_, _word2_
2.  PhrasCos2: _phrase_ \- _word2_, _word1_
3.  PhrasCos3: _phrase_, _word1_ \+ _word2_

The authors then used a small set of phrases (annotated as metaphorical or literal) to find an optimal classification threshold for each of the similarity metrics from above. The threshold was obtained and optimized by maximizing accuracy on this development set. Instances with values exceeding the threshold were considered literal with the remainder being considered metaphorical.

Experiments
-----------

### Annotated Datasets

The authors used two datasets that had been manually annotated for metaphoricity. The dataset from [Mohammed et al.](http://www.aclweb.org/anthology/S16-2003) used verbs from WordNet that had between three and ten senses and the sentences showing them off from their corresponding glosses. The way each of the verbs were used in the sentences (of which there were 1639) were each annotated by 10 annotators. Mohammad et al. then used the verbs that were tagged by 70% or more of the annotators as either metaphorical or literal as their dataset. The authors of the present paper further filtered that data by exclusively extracting verb-direct object and verb-subject relations from the dataset. This led to only 647 instances. 316 were metaphorical and 331 were literal. [Tsvetkov et al.](http://www.aclweb.org/anthology/W13-0906) on the other hand annotated adjective-noun pairs for metaphoricity rather than verb-noun pairs. They took a list of 1000 common adjectives and extracted nouns that each of the adjectives co-occurred with from the TenTen Web Corpus. Tsvetkov et al. then divided their dataset into training and testing sets. The training set had 1768 samples in total and the testing set had 222 samples in total. Each set had equal numbers of literal and metaphorical samples. Metaphorical phrases that depended on a larger context in order to be interpreted correctly were removed. The authors selected these two datasets specifically because each of the verbs and adjectives had multiple senses in which they could be used. This allows for testing on how well the model can discriminate between the different senses in which a word can be used rather than just selecting the word's most frequent classification.

### Experimental Setup

The authors then divided each of these datasets up into development (aka training) and test sets. The Mohammad et al. development set contained 80 items (even split of metaphorical and literal instances) with the remainder being used as the test set. The Tsvetkov et al. development set contained the same number extracted from the pre-made training set and they did not have to create a separate testing set since it had already been created. The authors experimented with the word-level similarity and all three phrase-level similarity inputs listed above. However, the first outperformed the other two during training so only the word-level and the first phrase-level similarity methods were used for testing. The word-level and the chosen phrase-level similarities were first evaluated using the the linguistic and visual representations in isolation. They were then evaluated with the multimodal vectors attained from the middle- and late-fusion strategies the authors implemented.

### Results and Discussion

The authors used three different evaluation metrics for their methods: precision, recall, and F-score. I took screenshots of each of the result tables: ![](http://tomlisankie.com/blog/wp-content/uploads/2018/04/Screen-Shot-2018-04-24-at-11.50.41-AM-300x220.png) ![](http://tomlisankie.com/blog/wp-content/uploads/2018/04/Screen-Shot-2018-04-24-at-11.48.36-AM-300x220.png) WordCos (with linguistic representations in isolation) outperforms PhrasCos1 by 17-19% on both verb-noun and adjective-noun pairs which suggests that information on the domains in which words belong is already captured successfully by the word embedding space. The opposite is true with visual data in isolation though. What PhrasCos1 measures is how semantically close the two words are that make up the phrase. In metaphorical language, there is a transfer of meaning and this ceases to be the case. Since there are no linguistic conventions or stylistic effects that take place with visual data, PhrasCos1 is better able to capture this effect. The trend was more clear with adjective-noun pairs than with verb-noun pairs. This suggests that identification of adjectival metaphors is highly informed by visual features. The multimodal methods outperformed both the isolated linguistic and visual models. This clearly demonstrates that visual features have some utility in metaphor detection. The MixLate late fusion method performs the best out of the fusion methods. This is probably because it fuses the highest performing similarity metrics for each respective representation (WordCos for linguistic representations and PhrasCos1 for visual representations). There are also a couple of good reasons for why there is a difference in performance on adjective-noun pairs and verb-noun pairs:

1.  [Previous psycholinguistic research](https://onlinelibrary.wiley.com/doi/full/10.1111/cogs.12076) suggests that people find it easier to make judgements on the concreteness of adjectives and nouns compared to verbs. Thus, the visual representations in these models may capture the concreteness of adjectives and nouns but may have trouble capturing the same qualities of verbs. The semantics of verbs are often grounded in motor activity and perception of that motor activity as opposed to being more static like adjectives and nouns.
2.  The visual data in the models in this paper are images rather than videos. Thus, perception of non-static actions is hindered.

Additionally, the authors performed another experiment where they evaluated the models on the larger portion of the training set from Tsvetkov et al. The trends observed were the same as with the smaller testing set. Something else noteworthy about this paper is that the authors were able to achieve pretty decent results with a fairly small development set for learning optimal thresholds. The reason this works is that necessary lexical knowledge, domain knowledge, and other properties are already encoded in the linguistic and visual embedding spaces. In order to see that these results weren't just some fluke, the authors split the training set of Tsvetkov et al. into 10 separate datasets that each contained around 170 samples and were balanced for metaphoricity (even numbers of literal and metaphorical samples). They trained the thresholds on 170 samples and then added an additional ~170 with each round of training. The thresholds turned out to be relatively stable with a standard deviation of only 0.03 for MixLate. Despite how few training samples the authors used, the results were comparable to those of other research that used far more data and used hand-annotated resources. The authors find this to be very encouraging.

Conclusion
----------

The authors of this paper presented the first method of using visual features in metaphor identification. Their results demonstrate that using multimodal representations outperform language-only representations. This suggests that visual information is very important in metaphor processing. Since the authors used a data-driven approach of learning visual features and linguistic features, possible noise from human annotation was avoided. Because of this, their methods can be applied to any text in any domain and easily reshaped for other metaphor processing tasks. The authors feel that it would be interesting in the future to apply similar multimodal embeddings to automatically interpret metaphorical language. They would also like to see automatic application of these embeddings in order to generalize associations between distinct concepts and domains.

* * *

Recreation
==========

In this section I outline the steps I took to recreate the paper and any pitfalls I may have experienced.

Acquiring the Data
------------------

The first step I had to take in order to begin the project was acquiring the data. The first dataset I acquired was the verb-noun dataset from Mohammad et al. It was not difficult to acquire since the link to the data was [directly available](http://saifmohammad.com/WebPages/metaphor.html) from the website of the named author. Finding the adjective-noun dataset was not as straightforward. The authors did not give a link to the completed dataset within their paper. I googled around a bit but could not find it listed anywhere. I decided to directly contact one of the authors instead ([Yulia Tsvetkov](http://www.cs.cmu.edu/~ytsvetko/)) who gladly provided me with the dataset. I should note though that Yulia pointed out to me that she believes the paper the authors were referencing was not the one they cited (namely "Cross-lingual metaphor detection using common semantic features") but instead a 2014 paper that had some similar phrasing in the title called "Metaphor Detection with Cross-Lingual Model Transfer" which is why I could not directly find the dataset myself even though it is available online. Next up was developing the corpus to train my Word2Vec model. I downloaded a recent dump of the English Wikipedia (the one the authors used is no longer available from Wikimedia because of its age, so close enough) and began to train a Word2Vec model using the [fastText](https://fasttext.cc) implementation as a test run since I've never trained a Word2Vec model from scratch before. However, I quickly realized that this was going to be a huge time suck and keep me from moving on to other parts of the paper at a reasonable rate since fastText was telling me it was going to take 11-12 hours to train this model and even then this was just a dummy model anyway. I was still going to have to do the preprocessing talked about in the paper of lemmatization, tagging, and parsing of the entire Wikipedia. Instead, I decided to contact the authors to see if they still had the corpus from this paper lying around. Turns out one of the authors ([Jean Maillard](http://www.maillard.it/)) did and he uploaded the corpus along with the scripts used to create it and a well-made README for me to download. Fantastic! The entire preprocessed Wiki, even with all the extra data from preprocessing, was far smaller (and streamable, yay) than the original Wiki dump I had downloaded because of being in the MessagePack format. So now I had instructions on how to use the exact corpus the authors used and didn't have to make my own. The only problem was I was gonna have to use a different implementation of Word2Vec.  

* * *

Analysis
========