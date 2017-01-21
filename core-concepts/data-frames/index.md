# Data Frames

Data Frames are used to store data during execution of an ML pipeline.
They are similar to a SQL table in that they have a schema for storing
the data types of every column and they have rows for storing the actual
values.

Spark, Scikit-learn, and MLeap all have their own version of a data
frame. Tensorflow uses a graph of inputs and outputs to execute
transformations, which is very easy to inteface with a data frame
structure.

## Spark Data Frames

Spark's data frames are optimized for distributed computation, making
them excellent for processing large datasets. They are very
heavy-weight, as they need to handle network failure scenarios,
compilation of execution plans, redundancy, and many other requirements
in a distributed context. Spark data frames offer a lot of functionality
outside of ML pipelines, such as joining large datasets, mapping,
reducing, SQL queries, etc.

## Scikit-learn Data Frames

Scikit-learn data frames are provided by [Pandas](http://pandas.pydata.org/)
and [NumPy](http://www.numpy.org/). These are lightweight data
structures, and offer quite a bit of the same functionality as Spark
data frames, minus the distributed nature of Spark's data frames.

## MLeap Data Frames: Leap Frames

Leap frames are very lightweight data structures and are meant to
support very basic operations and ML transformations. Because of their
simplicity, they are highly-optimized for use as a realtime prediction
engine or small-batch predictions. Leap frames can be abstracted over
Spark data frames, and so they do not lose their ability to act as an
efficient batch-mode data store as well.

## Tensorflow

[Tensorflow](https://www.tensorflow.org/) does not have data frames like Spark, Scikit-learn and MLeap.
Instead, Tensorflow relies on input nodes and output nodes, connected by
a graph of transfomation operations. This paradigm is actually neatly
compatible with data frames, as certain columns can be used to provide
data for the input nodes, while output nodes can be placed in new
columns of a data frame. Leap frames are specifically designed to be
compatible with Tensorflow graphs, Spark data frames, and to a certain
extent, Scikit-learn data frames.

