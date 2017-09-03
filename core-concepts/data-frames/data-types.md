# Data Types

There are many data types supported by Spark, MLeap, Scikit-learn and
Tensorflow. Fortunately, because all of these technologies are based on
well-known mathematical data structures, they are all cross-compatible
with each other to a large extent.

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
| Float | 32-bit floating point values are supported by all platforms |
| Double | 64-bit floating point values are supported by all platforms |
| Boolean | 8-bit value representing true or false, can be packed into 1-bit if needed |
| String | A series of characters, either null-terminated or length prefixed depending on platform |
| Array | Sequence of elements of any of the above basic types |
| Tensor | Supported by MLeap and Tensorflow, provides n-dimensional storage for one of the above basic data types |

