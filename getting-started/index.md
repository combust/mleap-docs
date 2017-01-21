# Getting Started

Whether using MLeap with PySpark, Scikit-learn, Spark, Tensorflow, or
MLeap Runtime, getting started is easy. For the most part, everything you will
need is already in [Maven Central](https://search.maven.org/) or
[PyPi](https://pypi.python.org/pypi). If you are trying to get up and
running with Tensorflow transformers, you will have to build some of
your own jars.

Experimenting with MLeap is easy to do either through an interactive
Scala console, Python console or a notebook.


## Typical MLeap Workflow

A typical MLeap workflow consists of 3 parts:
1. Training: Write your ML Pipelines the same way you do today
2. Serialization: Serialize all of the data processing (ml pipeline) and the algorithms to Bundle.ML
3. Execution: Use MLeap runtime to execute your serialized pipeline without dependencies on Spark or Scikit (you'll still need TensorFlow binaries)

### Training

MLeap is designed to have minimal impact on how you build your ML pipelines today. 
We want you to write your scala or python code in the same way you do today, with minimial additional needed to support MLeap functionality.

As you will see from the basic usage section, most often all you have to do is import some MLeap libraries and that is it (except for scikit-learn)*[]: 


### Serialization

Once you have your pipeline trained, MLeap provides functionality to serialize the entire ML/Data Pipeline and your trained algorithm (linear models, tree-based models, neural networks) to Bundle.ML.
Serialization generates something called a `bundle` which is a physical representation of your pipeline and algorithm that you can deploy, share, view all of the pieces of the pipeline.


### Execution

The goal of MLeap was initially to enable scoring of Spark's ML pipelines without the dependency on Spark. That functionality is powered by MLeap Runtime, which loads your serialized bundle and executes it on incoming dataframes (LeapFrames).

Did we mention that MLeap Runtime is extremely fast? We have recorded benchmarks of micro-second execution on LeapFrames and sub-5ms response times when part of a RESTful API service.

Note: As of right now, MLeap runtime is only provided as a Java/Scala library, but we do plan to add python bindings in the future.