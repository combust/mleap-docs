# MLeap Bundles

MLeap Bundles are a portable file format for serializing and
deserializing:
1. Machine learning data pipelines - any transformer-based data pipeline
2. Algorithms (Regressions, Tree-Based models, Bayesian models, Neural Nets, Clustering)

Bundles make it very easy to share the results of your training, simply generate a bundle file and
send it through email to a colleague or just view the metadata of your data pipeline and algorithm! 

Bundles also make deployments simple: just export your bundle and load it into your Spark,
Scikit-learn, or MLeap-based application.

## Features of MLeap Bundles

1. Serialize to a directory or a zip file
2. Entirely JSON and Protobuf-based format
3. Serialize as pure JSON, pure Protobuf, or mixed mode
4. Highly extensible, including support for custom data types and easy
   integration with new transformers

## MLeap Bundle Examples

Here are some examples of serialized bundle files. They are not meant to
be useful pipelines, but rather to illustrate what these files actually
look like. The pipelines were generated when running our Spark parity
tests, which ensure that MLeap transformers and Spark transformers
produce exactly the same outputs.

<a href="../assets/bundles/spark-parity.zip" download>MLeap/Spark Parity Bundle Examples</a>
