# Data Types

There are many data types supported by Spark, MLeap, Scikit-learn and
Tensorflow. Fortunately, because all of these technologies are based on
well-known mathematical data structures, they are all cross-compatible
with each other to a large extent. All of these technologies also offer
options for extending the set of allowed data types through the use of
custom data types.

Data frames store the data types of their columns in a schema object.
This schema can be consulted to determine which operations are available
for which columns and how transformations should be handled.

## Supported Data Types

| Data Type | Notes |
|---|---|
| Byte | 8-bit integer values supported by all platforms, MLeap and Spark only support signed versions |
| Short | 16-bit integer values supported by all platforms, MLeap and Spark only support signed versions |
| Integer | 32-bit integer values supported by all platforms, MLeap and Spark only support signed versions |
| Long | 64-bit integer values are supported by all platforms, MLeap and Spark only support signed versions |
| Float | 32-bit folating point values are supported by all platforms |
| Double | 64-bit folating point values are supported by all platforms |
| Boolean | 8-bit value representing true or false, can be packed into 1-bit if needed |
| String | A series of characters, either null-terminated or length prefixed depending on platform |
| Array | Sequence of elements of any of the above basic types |
| Tensor | Supported by MLeap and Tensorflow, provides n-dimensional storage for one of the above basic data types |
| Custom | Custom data types are supported by MLeap, Spark, and Scikit-learn. |

These are all of the data types needed to produce very complex ML
transformations and pipelines. We could build entire libraries around
custom data types, such as an image, sound, or video file. These
libraries of transformers can be used to extract features from these
custom types and then train machine learning models to make predictions
on future data. Because all of these platforms support similar data
types, data frames, and transformers, there is not reason these
libraries of transformers shouldn't work on any of them!

