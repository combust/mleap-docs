<img src="assets/images/logo.png" alt="MLeap Logo" width="176" height="70" />

MLeap is a common runtime and serialization format for entire machine learning pipelines. It supports Spark, Scikit-learn and Tensorflow for training pipelines and exporting them to an MLeap Bundle. The pipeline can then be deserialized into a Spark ML pipeline, Scikit-learn pipeline, Tensorflow graph, or MLeap pipeline for use as a scoring engine or general purpose transformer pipeline. MLeap itself offers a comprehensive engine for executing pipelines that is portable across the JVM.

## Why MLeap?

Many companies that use Spark and Scikit-learn have a difficult time
deploying their models to production API services. Even using Tensorflow
can be difficult to set these services up if a company does not wish to
use Python in their API stack or does not use Google ML Cloud. MLeap
provides simple interfaces to executing entire ML pipelines, from
feature transformers to classifiers, regressions, clustering algorithms,
and neural networks.

### Portable Models

Your models are your models. Take them with you wherever you go using
our MLeap Bundles. Platforms like Microsoft Azure and Google ML can lock
you into their services package. MLeap allows you to take your models
with you wherever you go.

### Spark, Scikit-learn and Tensorflow: One Runtime

Mixing and matching ML technologies becomes a simple task. Instead of requiring
and entire team of developers to make your pipelines production ready,
simply export to an MLeap Bundle and run your pipeline wherever it is
needed. Train different pieces of your pipeline using Spark,
Scikit-learn or Tensorflow, then export them to one MLeap Bundle file
and deploy it anywhere.

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

