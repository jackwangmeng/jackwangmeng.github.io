---
layout: post
title: 词嵌入
description: word embedding
category: 深度学习
---
## 词嵌入

word embedding的意思是：给出一个文档，文档就是一个单词序列比如 “A B A C B F G”, 希望对文档中每个不同的单词都得到一个对应的向量
(往往是低维向量)表示。比如，对于这样的“A B A C B F G”的一个序列，也许我们最后能得到：A对应的向量为[0.1 0.6 -0.5]，B对应的向量为[-0.2 0.9 0.7]  
（此处的数值只用于示意）之所以希望把每个单词变成一个向量，目的还是为了方便计算，比如“求单词A的同义词”，就可以通过“求与单词A在cos距离下最相似的向量”来做到。
word embedding不是一个新的topic，很早就已经有人做了，比如bengio的paper“Neural probabilistic language models”，这其实还不算最早，更早的时候，
Hinton就已经提出了distributed representation的概念“Learning distributed representations of concepts”(只不过不是用在word embedding上面) ，
AAAI2015的时候问过Hinton怎么看google的word2vec，他说自己20年前就已经搞过了，哈哈，估计指的就是这篇paper。总之，
常见的word embedding方法就是先从文本中为每个单词构造一组features，然后对这组feature做distributed representations，
哈哈，相比于传统的distributed representations，区别就是多了一步(先从文档中为每个单词构造一组feature)。既然word embedding是一个老的topic，
为什么会火呢？原因是Tomas Mikolov在Google的时候发的这两篇
paper：“Efficient Estimation of Word Representations in Vector Space”、“Distributed Representations of Words and Phrases and their Compositionality”。
这两篇paper中提出了一个word2vec的工具包，里面包含了几种word embedding的方法，这些方法有两个特点。一个特点是速度快，另一个特点是得到的embedding vectors具备analogy性质。
analogy性质类似于“A-B=C-D”这样的结构，举例说明：“北京-中国 = 巴黎-法国”。Tomas Mikolov认为具备这样的性质，则说明得到的embedding vectors性质非常好，能够model到语义。
这两篇paper是2013年的工作，至今(2015.8)，这两篇paper的引用量早已经超好几百，足以看出其影响力很大。当然，word embedding的方案还有很多，常见的## ## word embedding的方法有:
1. Distributed Representations of Words and Phrases and their Compositionality
2. Efficient Estimation of Word Representations in Vector Space
3. GloVe Global Vectors forWord Representation
4. Neural probabilistic language models
5. Natural language processing (almost) from scratch
6. Learning word embeddings efficiently with noise contrastive estimation
7. A scalable hierarchical distributed language model
8. Three new graphical models for statistical language modelling
9. Improving word representations via global context and multiple word prototypesword2vec中的模型至今(2015.8)还是存在不少未解之谜，
## 因此就有不少papers尝试去解释其中一些谜团，或者建立其与其他模型之间的联系，
下面是paper list
1. Neural Word Embeddings as Implicit Matrix Factorization
2. Linguistic Regularities in Sparse and Explicit Word Representation
3. Random Walks on Context Spaces Towards an Explanation of the Mysteries of Semantic Word Embeddings
4. word2vec Explained Deriving Mikolov et al.’s Negative Sampling Word Embedding Method
5. Linking GloVe with word2vec6. Word Embedding Revisited: A New Representation Learning and Explicit Matrix Factorization Perspective
