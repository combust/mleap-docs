# Transformers

Transformers offer a basic building block to executing machine learning
pipelines. A transformer takes data from a data frame, transforms it
using some operation and outputs one or more new fields to back to the
data frame. This is a very simple task, but is at the core of the Spark,
Scikit-learn, MLeap and Tensorflow execution engines.

Transformers are used for many different tasks, but the most common in
machine learning are:

1. Feature extraction
2. Model scoring

## Feature Extraction

Feature extraction is the process of taking one of more features from
and input dataset and deriving new features from them. In the case of
data frames, the features come from the input data frame and are written
to the output data frame.

Some examples of feature extraction are:

1. String indexing (label encoding) - Taking a string and converting it
   to an integer value
2. One hot encoding - Converting an integer value to a vector of 1s and
   0s
3. Feature selection - Running analysis to determine which features are
   most effective for training a predictive ML algorithm (PCA, CHI2)
4. Math - Basic mathematical functions, such as dividing two features by
   each other or taking the log of a feature

There are too many examples of feature extraction to enumerate here, but
take a look at our complete list of [supported feature transformers](support.html#features) to
get an idea of what is possible.

## Regression

Regression is used to predict a continuous
numeric value, such as the price of a car or a home. Regression models,
for the most part, operate on vectors of doubles called a "feature
vector". The feature vector contains all of the known information about
what is being predicted. In the case of predicting a price of a house,
the feature vector will have things like the encoded region where the
house is, the square footage, how many bathrooms there are, how old it
is, etc.

See a list of [supported regression models](support.html#regression).

## Classification

Classification is used to predict categorical information. An example is
making a binary prediction of whether or not to give a consumer a loan
or not. Another example is predicting what type of sound is contained in
a wav file, or whether or not there is a person in and image. There are
many algorithms for classification, and neural networks are really great
at this task.

[Supported classification models](support.html#classification).

## Clustering

Clustering is used to assign a label to similar data. It is similar to
classification in that the predictions are discrete values from a set.
Unlike classification models though, clustering models do not know what
the labels are ahead of time and instead invent their own set of labels
during training. This is useful for anomaly detection, feature
generation, as well as many other useful tasks.


[Supported clustering models](support.html#clustering).

## Other Types of Transformers

Transformers can do ANYTHING! This is just a sample of the most common
uses of them. However, you can build transformers to resize images,
resample sound data, import data from different data sources or anything
else you can think of. The sky is the limit.
