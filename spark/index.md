# MLeap Spark Integration

MLeap's Spark integration comes with the following feature set:
* Serialization/Deserialization of Transformers and Pipelines to and from Bundle.ML
* Support of additional feature transformers and models (ex: SVM, OneVsRest, MapTransform)
* Support for custom transformers

To use MLeap you do not have to change how you construct your existing pipelines, so the rest of the documentation is going to focus on how to serialize and deserialize your pipeline to and from bundle.ml.
To see how to execute your pipeline outside of Spark, refer to the [MLeap Runtime](../mleap-runtime/index.md) section.

# Serializing with Spark

Serializing and deserializing with Spark works almost exactly the same
as with MLeap. The only difference is we are serializing and
deserializing Spark pipelines and we need to import different implicit
support classes.

## Create a Simple Spark Pipeline

```scala
import ml.combust.bundle.BundleFile
import ml.combust.bundle.serializer.SerializationFormat
import org.apache.spark.ml.feature.{StringIndexerModel, VectorAssembler}
import org.apache.spark.ml.mleap.SparkUtil
import ml.combust.mleap.spark.SparkSupport._
import resource._

// Create a sample pipeline that we will serialize
// And then deserialize using various formats
val stringIndexer = new StringIndexerModel(labels = Array("Hello, MLeap!", "Another row")).
  setInputCol("a_string").
  setOutputCol("a_string_index")
val featureAssembler = new VectorAssembler().setInputCols(Array("a_double")).
  setOutputCol("features")

// Because of Spark's privacy, our example pipeline is considerably
// Less interesting than the one we used to demonstrate MLeap serialization
val pipeline = SparkUtil.createPipelineModel(Array(stringIndexer, featureAssembler))
```

## Serialize to Zip File

In order to serialize to a zip file, make sure the URI begins with
`jar:file` and ends with a `.zip`.

For example
`jar:file:/tmp/mleap-bundle.zip`.

### JSON Format

```scala
for(bundle <- managed(BundleFile("jar:file:/tmp/mleap-examples/simple-json.zip"))) {
  pipeline.writeBundle.format(SerializationFormat.Json).save(bundle)
}
```

### Protobuf Format

```scala
for(bundle <- managed(BundleFile("jar:file:/tmp/mleap-examples/simple-protobuf.zip"))) {
  pipeline.writeBundle.format(SerializationFormat.Protobuf).save(bundle)
}
```

### Mixed Format

```scala
for(bundle <- managed(BundleFile("jar:file:/tmp/mleap-examples/simple-mixed.zip"))) {
  pipeline.writeBundle.format(SerializationFormat.Mixed).save(bundle)
}
```

## Serialize to Directory

In order to serialize to a directory, make sure the URI begins with
`file`.

For example `file:/tmp/mleap-bundle-dir`

### JSON Format

```scala
for(bundle <- managed(BundleFile("file:/tmp/mleap-examples/simple-json-dir"))) {
  pipeline.writeBundle.format(SerializationFormat.Json).save(bundle)
}
```

### Protobuf Format

```scala
for(bundle <- managed(BundleFile("file:/tmp/mleap-examples/simple-protobuf-dir"))) {
  pipeline.writeBundle.format(SerializationFormat.Protobuf).save(bundle)
}
```

### Mixed Format

```scala
for(bundle <- managed(BundleFile("file:/tmp/mleap-examples/simple-mixed-dir"))) {
  pipeline.writeBundle.format(SerializationFormat.Mixed).save(bundle)
}
```

## Deserializing

Deserializing is just as easy as serializing. You don't need to know the
format the MLeap Bundle was serialized as beforehand, you just need to
know where the bundle is.

### From Zip Bundle

```scala
// Deserialize a zip bundle
// Use Scala ARM to make sure resources are managed properly
val zipBundle = (for(bundle <- managed(BundleFile("jar:file:/tmp/mleap-examples/simple-mixed.zip"))) yield {
  bundle.loadSparkBundle().get
}).opt.get
```

### Directory Bundle

```scala
// Deserialize a directory bundle
// Use Scala ARM to make sure resources are managed properly
val dirBundle = (for(bundle <- managed(BundleFile("file:/tmp/mleap-examples/simple-mixed-dir"))) yield {
  bundle.loadSparkBundle().get
}).opt.get
```
