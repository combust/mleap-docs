# Tensorflow MLeap Transformer

MLeap supports integration with pre-trained Tensorflow graphs. MLeap uses the Swig bindings provided with Tensorflow to call down to Tensorflow C++ libraries after converting from MLeap data types to the required C++ tensor types.

## MLeap Tensorflow Transformer

The MLeap Tensorflow transformer is built with a Tensorflow graph file that was saved using the `freeze_graph` function. When the transformer lazily initializes a Tensorflow sessions on the first request to transform a leap frame. Once the sessions is started, it remains open until the `close` method is called on the MLeap Tensorflow transformer.

The data in the LeapFrame should be in the proper format when handed to the Tensorflow transformer, so that the proper conversions to Tensorflow data types occurs. See the [Type Conversions](#type-conversions) section for how converting to Tensorflow types works.

The transformer then outputs a list of tensors as the raw Tensorflow output field, along with individual columns for all output tensors specified. The intermediate output list is needed because of the way MLeap works currently, but we may get rid of it in the future. See [Example](#example) for an example of how a Tensorflow graph can be executed against a LeapFrame. The output types of the tensors are needed ahead of time so that we know how to form the new LeapFrame after transforming.

### MLeap Tensorflow Model

The model is responsible for maintaining the Tensorflow session. It is a Java `Closeable` and needs to be closed when the MLeap transformer or other controlling object no longer needs use of the resources. The model will close the Tensorflow sessions when finalized, just in case the user forgets to call this method. The output of the model is a `Seq` of MLeap `Tensor` that contains the outputs in the same order specified by the given shape.

In order to support transformations using Tensorflow with MLeap data types, the following information is needed.

| Attribute | Description |
|---|---|
| graph | The Tensorflow GraphDef saved with __freeze_graph__ |
| shape | Inputs/outputs along with non-optional type specification |
| nodes | String list of nodes to run without saving output |

## Type Conversions

All basic data types have implicit conversions to Tensorflow tensors of rank 0. The `Tensor` data type in MLeap converts pretty much one-to-one with Tensorflow tensors. `SparseTensor` values are first converted to dense tensors before being passed in to Tensorflow.

Here is the list of currently-supported Tensorflow data types.

| Tensorflow Data Type | MLeap Data Type |
|---|---|
| DT_BOOLEAN | BooleanType |
| DT_STRING | StringType |
| DT_INT32 | IntegerType |
| DT_INT64 | LongType |
| DT_FLOAT | FloatType |
| DT_DOUBLE | DoubleType |
| _Tensor_ | TensorType |

Because the Swig wrappers do not provide support for all data types, we recommend adding in casting steps to your Tensorflow graph to integrate with MLeap.

### Notes

1. Unsigned integer types, 8-bit, and 16-bit integers are not supported by the Swig wrappers
2. Complex types are not supported by the Swig wrappers

# Example

Say we have an input LeapFrame that has the following data. A scala double and a 1-dimensional double tensor (also known as a vector of doubles).

## Input Leap Frame

### Data


| double1 | tensor1 |
|---|---|
| 3.0 | [2.0, 1.0, 5.0] |

### Types

| Field | Data Type |
|---|---|
| double1 | DoubleType() |
| tensor1 | TensorType(DoubleType(false)) |

If we run this LeapFrame through a Tensorflow transformer that uses the `double1` value to scale the `tensor1` value and we specify we want the scaled output tensor, then the output LeapFrame will look like this:

## Output Leap Frame

### Data

| double1 | tensor1 | raw_tf_tensors | output1 |
|---|---|---|---|
| 3.0 | [2.0, 1.0, 5.0] | Seq([6.0, 3.0, 15.0]) | [6.0, 3.0, 15.0] |

### Types

| Field | Data Type | Notes |
|---|---|---|
| double1 | DoubleType() | |
| tensor1 | TensorType(DoubleType(false)) | |
| raw_tf_tensors | ListType(AnyType(false))  | Raw return types unknown, nulls are not allowed |
| output1 | TensorType(DoubleType(false)) | Underlying type known at this point, and reflected in the LeapFrame |

## Notes

1. The `raw_tf_tensors` field contains an `AnyType`, which means that it is not serializable. If using Combust API servers, you will have to filter this value out when retrieving results.

