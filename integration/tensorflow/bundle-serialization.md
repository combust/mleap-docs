# Tensorflow Bundle Serialization

In order to provide tight integration with Tensorflow, MLeap will
provide its own set of C++ ops that correspond to MLeap operations. We
will use ops that are already provided as much as possible, but may need
to implement some custom ones for full interoperability.

## Compatible Ops

We maintain a [list of supported ops](../../core-concepts/transformers/support.html) between Spark, MLeap, Scikit-learn
and Tensorflow.

## Serialization

In addition to providing the library of Tensorflow ops, we will also
provide a way to serialize Tensorflow graphs to Bundle.ML and
deserialize Bundle.ML to Tensorflow graphs. This will allow for the
direct export of Spark, Scikit-learn, and MLeap pipelines to Tensorflow
for use on mobile devices and other applications.

