---
layout: post
date: 2016-12-22
title: "NLP: Classification using a Naive Bayes classifier"
description: "Spam vs. Ham sms: a text classification using a Naive Bayes Classifier #nlp #textmining #naivebayes #classification #supervised #unstructureddata"
---

[Here](http://rpubs.com/pparacch/237109) is possible to find the __application of the Naive Bayes approach__ to a specific problem: the classification of SMS into __spam__ ("an undesired messages, e.g. advertising") or __ham__ ("a desired message containing valuable information that is not considered spam”). The supporting code can be found [here](https://github.com/pparacch/DataSciencePosts/blob/master/exp_NLP_NaiveBayes_example.Rmd).

The data used for such playground activity is the [SMS Spam Collection v. 1](http://www.dt.fee.unicamp.br/~tiago/smsspamcollection/), a public set of SMS messages that have been collected for mobile phone spam research where each message has been properly labeled as __spam__ or __ham__.

### Background information

__Q:__ What is classification?

_'In machine learning and statistics, classification is the problem of identifying to which of a set of categories (sub-populations) a new observation belongs, on the basis of a training set of data containing observations (or instances) whose category membership is known. An example would be assigning a given email into "spam" or "non-spam" classes or assigning a diagnosis to a given patient as described by observed characteristics of the patient (gender, blood pressure, presence or absence of certain symptoms, etc.). Classification is an example of pattern recognition.'_ [wikipedia](https://en.wikipedia.org/wiki/Statistical_classification)

Classification is a type of __supervised learning__ problem.

__Q__: What is supervised learning?

_'Supervised learning is the machine learning task of inferring a function from labeled training data.[1] The training data consist of a set of training examples. In supervised learning, each example is a pair consisting of an input object (typically a vector) and a desired output value (also called the supervisory signal). A supervised learning algorithm analyzes the training data and produces an inferred function, which can be used for mapping new examples.'_ [wikipedia](https://en.wikipedia.org/wiki/Supervised_learning)

__Q__: What is a Naive Bayes classifier?

_'Naive Bayes classifiers are a family of simple probabilistic classifiers based on applying Bayes' theorem with strong (naive) independence assumptions between the features.'_ [wikipedia](https://en.wikipedia.org/wiki/Naive_Bayes_classifier)

To have a better overview and understanding of the theory behind text classification & Naive Bayes classifiers, the material created by __Dan Jurafsky & Christopher Manning__ for the __"Natural Language Processing" MOOC__ at __Coursera__ is a great starting point

* [Slides around Text Classification & Naive Bayes](http://spark-public.s3.amazonaws.com/nlp/slides/naivebayes.pdf)


* Lesson Videos
  * [What is Text Classification](https://www.youtube.com/watch?v=c3fnHA6yLeY&list=PL6397E4B26D00A269&index=24)
  * [Naive Bayes](https://www.youtube.com/watch?v=DdYSMwEWbd4&list=PL6397E4B26D00A269&index=25)
  * [Formalizing the Naive Bayes classifier](https://www.youtube.com/watch?v=TpjPzKODuXo&list=PL6397E4B26D00A269&index=26)
  * [Naive Bayes Learning](https://www.youtube.com/watch?v=0hxaqDbdIeE&list=PL6397E4B26D00A269&index=27)
  * [Naive Bayes & Language Modeling](https://www.youtube.com/watch?v=ALna9TjBS8Q&list=PL6397E4B26D00A269&index=28)
  * [Multinomial Naive Bayes example](https://www.youtube.com/watch?v=pc36aYTP44o&t=2s&list=PL6397E4B26D00A269&index=29)
  * [Precision, Recall & F measure](https://www.youtube.com/watch?v=2akd6uwtowc&list=PL6397E4B26D00A269&index=30)
  * [Text Classification Evaluation](https://www.youtube.com/watch?v=OwwdYHWRB5E&list=PL6397E4B26D00A269&index=31)  



The "[Speech and Language Processing](https://web.stanford.edu/~jurafsky/slp3/)" book of __Dan Jurafsky__ & __James H. Martin__

* "[Naive Bayes and Sentiment
Classification](https://web.stanford.edu/~jurafsky/slp3/6.pdf)" chapter

Enjoy the learnings...
