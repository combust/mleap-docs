# MLeap PySpark Integration

MLeap's PySpark integration comes with the following feature set:
* Serialization/Deserialization of Transformers and Pipelines to and from Bundle.ML
* Support of additional feature transformers and models (ex: SVM, OneVsRest, MapTransform)
* Support for custom transformers

To use MLeap you do not have to change how you construct your existing pipelines, so the rest of the documentation is going to focus on how to serialize and deserialize your pipeline to and from bundle.ml.
To see how to execute your pipeline outside of Spark, refer to the [MLeap Runtime](../mleap-runtime/index.md) section.

# Serializing with PySpark

Serializing and deserializing with PySpark works almost exactly the same
as with MLeap. The only difference is we are serializing and
deserializing Spark pipelines and we need to import different support classes.

## Create a Simple Spark Pipeline

```python
# Imports MLeap serialization functionality for PySpark
import mleap.pyspark
from mleap.pyspark.spark_support import SimpleSparkSerializer

# Import standard PySpark Transformers and packages
from pyspark.ml.feature import VectorAssembler, StandardScaler, OneHotEncoder, StringIndexer
from pyspark.ml import Pipeline, PipelineModel
from pyspark.sql import Row

# Create a test data frame
l = [('Alice', 1), ('Bob', 2)]
rdd = sc.parallelize(l)
Person = Row('name', 'age')
person = rdd.map(lambda r: Person(*r))
df2 = spark.createDataFrame(person)
df2.collect()

# Build a very simple pipeline using two transformers
string_indexer = StringIndexer(inputCol='name', outputCol='name_string_index')

feature_assembler = VectorAssembler(inputCols=[string_indexer.getOutputCol()], outputCol="features")

feature_pipeline = [string_indexer, feature_assembler]

featurePipeline = Pipeline(stages=feature_pipeline)

featurePipeline.fit(df2)

```


## Serialize to Zip File

In order to serialize to a zip file, make sure the URI begins with
`jar:file` and ends with a `.zip`.

For example
`jar:file:/tmp/mleap-bundle.zip`.

### JSON Format

```python
featurePipeline.serializeToBundle("jar:file:/tmp/pyspark.example.zip")
```

### Protobuf Format

Support coming soon

## Deserializing

Deserializing is just as easy as serializing. You don't need to know the
format the MLeap Bundle was serialized as beforehand, you just need to
know where the bundle is.

### From Zip Bundle

```python
featurePipeline = PipelineModel.deserializeFromBundle("jar:file:/tmp/pyspark.example.zip")
```