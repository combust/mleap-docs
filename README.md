<img src="assets/images/logo.png" alt="MLeap Logo" width="176" height="70" />

#### What is MLeap?

MLeap is a common serialization format and execution engine for machine learning pipelines. It supports Spark, Scikit-learn and Tensorflow for training pipelines and exporting them to an MLeap Bundle. Serialized pipelines (bundles) can be deserialized back into Spark, Scikit-learn, TensorFlow graphs, or an MLeap pipeline for use in a scoring engine (API Servers).

## Why MLeap?

Many companies that use Spark and Scikit-learn have a difficult time
deploying their research ML/data pipelines models to production API services. Even using Tensorflow
can be difficult to set these services up if a company does not wish to
use Python in their API stack or does not use Google ML Cloud. MLeap
provides simple interfaces to execute entire ML pipelines, from
feature transformers to classifiers, regressions, clustering algorithms,
and neural networks.

### Portable Models

Your models are your models. Take them with you wherever you go using
MLeap Bundles. Platforms like Microsoft Azure and Google ML can lock
you into their services package. MLeap allows you to take your models
with you wherever you go.

### Spark, Scikit-learn and Tensorflow: One Runtime

Mixing and matching ML technologies becomes a simple task. Instead of requiring
an entire team of developers to make research pipelines production ready,
simply export to an MLeap Bundle and run your pipeline wherever it is
needed.

Other benefits of a unified runtime:
* Train different pieces of your pipeline using Spark,
Scikit-learn or Tensorflow, then export them to one MLeap Bundle file
and deploy it anywhere
* If you're using Scikit for R&D, but Spark comes out with a better algorithm,
you can export your Scikit ML pipeline to Spark, train the new model in Spark
and then deploy to production using the MLeap runtime

<img src="assets/images/single-runtime.jpg" alt="Unified Runtime"/>

### Common Serialization

In addition to providing a useful execution engine, MLeap Bundles
provide a common serialization format for a large set of ML feature
extractors and algorithms that are able to be exported and imported
across Spark, Scikit-learn, Tensorflow and MLeap. This means you can
easily convert pipelines between these technologies depending on where
you need to execute a pipeline.

### Seamless Integrations

For the most part, we do not modify any internal code or require custom
implementations of transformers in any Spark or Scikit-learn. For
Tensorflow, we use as many builtin ops as we can and implement custom
ops for MLeap when they do not exist. This means that code changes to
your existing pipelines are minimal to get up and running with MLeap.
For many use cases, no changes will be required and you can simply
export to an MLeap Bundle or deploy to a Combust API server to start
getting immediate use of your pipeline.

### Open Source

MLeap is entirely open source. Our source code is available at
[https://github.com/combust/mleap](https://github.com/combust/mleap). We also automate
our tests and deploys with [travis ci](https://travis-ci.org/combust/mleap).

