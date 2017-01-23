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

## Bundle Structure


## Common Format For Spark, Scikit-Learn, TensorFlow

MLeap provides a serialization format for common transformers that are found in Spark, Scikit and TF. For example, consider the Standard Scaler trasnformer (`tf.random_normal_initializer` in TensorFlow). It performs the same opperation on all three platforms so in theory can be serialized, deserialized and used interchangeably between them.

<img src="../assets/images/common-serialization.jpg" alt="Common Serialization"/>
Example of model.json for `StandardScaler`

```json
{
   "op": "standard_scaler",
   "attributes": [
      {
         "type": {
            "type": "tensor",
            "tensor": {
               "base": "double",
               "dimensions": [-1]
            }
         },
         "name": "mean",
         "value": [0.323422, 1.320421]
      },
      {
         "type": {
            "type": "tensor",
            "tensor": {
               "base": "double",
               "dimensions": [-1]
            }
         },
         "name": "std",
         "value": [0.0422943, 0.452913]
      }
   ]
}
```

Example of node.json for `StandardScaler`

```json
{
   "name": "standard_scaler_e82ff159-df94-11e6-94a5-acbc329465af",
   "shape": {
      "inputs": [
         {
            "name": "unscaled_cont_features",
            "port": "input"
         }
      ],
      "outputs": [
         {
            "name": "scaled_cont_features",
            "port": "output"
         }
      ]
   }
}
```


## MLeap Bundle Examples

Here are some examples of serialized bundle files. They are not meant to
be useful pipelines, but rather to illustrate what these files actually
look like. The pipelines were generated when running our Spark parity
tests, which ensure that MLeap transformers and Spark transformers
produce exactly the same outputs.

<a href="../assets/bundles/spark-parity.zip" download>MLeap/Spark Parity Bundle Examples</a>

NOTE: right click and "Save As...", Gitbook prevents directly clicking
on the link.
