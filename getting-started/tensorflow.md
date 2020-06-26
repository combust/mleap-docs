# Getting Started with Tensorflow

MLeap Tensorflow integration provides support for including Tensorflow
graphs as a transform step in your ML pipelines. In the future we may
provide more compatibility. For right now, Tensorflow integration should
be considered experimental as both Tensorflow and MLeap integration with
Tensorflow are still stabilizing.

## Building MLeap-Tensorflow

MLeap Tensorflow modules are not included in maven central, and must
instead be built from source along with the Tensorflow JNI support. See
[instructions](building.md#build-tensorflow-mleap-module) for building
the Tensorflow module.

## Using MLeap-Tensorflow

Once you have everything built, it's easy to incorporate Tensorflow into
your MLeap pipelines.

First, include the module as a project dependency:

```sbt
libraryDependencies += "ml.combust.mleap" %% "mleap-tensorflow" % "0.16.0"
```

Then we can start using Tensorflow graphs, let's build a simple one that
multiplies two tensors:

```scala
import ml.combust.mleap.core.types._
import ml.combust.mleap.runtime.frame.{DefaultLeapFrame, Row}
import ml.combust.mleap.tensor.Tensor
import ml.combust.mleap.tensorflow.{TensorflowModel, TensorflowTransformer}
import org.tensorflow

// Initialize our Tensorflow demo graph
val graph = new tensorflow.Graph

// Build placeholders for our input values
val inputA = graph.opBuilder("Placeholder", "InputA").
  setAttr("dtype", tensorflow.DataType.FLOAT).
  build()
val inputB = graph.opBuilder("Placeholder", "InputB").
  setAttr("dtype", tensorflow.DataType.FLOAT).
  build()

// Multiply the two placeholders and put the result in
// The "MyResult" tensor
graph.opBuilder("Mul", "MyResult").
  setAttr("T", tensorflow.DataType.FLOAT).
  addInput(inputA.output(0)).
  addInput(inputB.output(0)).
  build()

// Build the MLeap model wrapper around the Tensorflow graph
val model = TensorflowModel(graph,
  // Must specify inputs and input types for converting to TF tensors
  inputs = Seq(("InputA", TensorType.Float()), ("InputB", TensorType.Float())),
  // Likewise, specify the output values so we can convert back to MLeap
  // Types properly
  outputs = Seq(("MyResult", TensorType.Float())))

// Connect our Leap Frame values to the Tensorflow graph
// Inputs and outputs
val shape = NodeShape().
  // Column "input_a" gets sent to the TF graph as the input "InputA"
  withInput("InputA", "input_a").
  // Column "input_b" gets sent to the TF graph as the input "InputB"
  withInput("InputB", "input_b").
  // TF graph output "MyResult" gets placed in the leap frame as col
  // "my_result"
  withOutput("MyResult", "my_result")

// Create the MLeap transformer that executes the TF model against
// A leap frame
val transformer = TensorflowTransformer(shape = shape, model = model)

// Create a sample leap frame to transform with the Tensorflow graph
val schema = StructType(StructField("input_a", ScalarType.Float), StructField("input_b", ScalarType.Float)).get
val dataset = Seq(Row(5.6f, 7.9f),
  Row(3.4f, 6.7f),
  Row(1.2f, 9.7f))
val frame = DefaultLeapFrame(schema, dataset)

// Transform the leap frame and make sure it behaves as expected
val data = transformer.transform(frame).get.dataset
assert(data(0)(2).asInstanceOf[Tensor[Float]].get(0).get == 5.6f * 7.9f)
assert(data(1)(2).asInstanceOf[Tensor[Float]].get(0).get == 3.4f * 6.7f)
assert(data(2)(2).asInstanceOf[Tensor[Float]].get(0).get == 1.2f * 9.7f)

// Cleanup the transformer
// This closes the TF session and graph resources
transformer.close()
```

For more information on how Tensorflow integration works:

1. Details on data conversion and integration [here](../tensorflow/mleap-integration.md).
2. How we serialize MLeap bundles with Tensorflow graphs [here](../tensorflow/bundle-serialization.md)
