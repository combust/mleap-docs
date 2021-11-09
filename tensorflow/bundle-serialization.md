# Tensorflow Bundle Serialization

When serializing your Tensorflow transformers to an MLeap Bundle, we
will store the Tensorflow graph as a Protobuf file. In order to
Tensorflow graphs, you should first freeze your Tensorflow graph using
[freeze_graph](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/python/tools/freeze_graph.py).
This will ensure that everything needed to execute your graph is in the
graph definition file.

## Sample MLeap Tensorflow Bundle

Download an example MLeap Bundle that uses Tensorflow to add two floats
together: [MLeap Tensorflow Bundle](../assets/bundles/tensorflow-bundle.zip).

NOTE: right click and "Save As...", Gitbook prevents directly clicking
on the link.
