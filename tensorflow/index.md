# Tensorflow

MLeap provides integration with Tensorflow in 2 forms:

1. An MLeap Tensorflow transoformer that executes arbitrary Tensorflow graphs
2. A set of custom operations within Tensorflow that can execute MLeap graphs

Currently only __1__ is supported, we are working on implementing __2__,
which will make it possible to export your Scikit-learn, Spark, or MLeap
pipeline directly to Tensorflow.

