# Serializing with MLeap

Serializing and deserializing with MLeap is a simple task. You can
choose to serialize to a directory on the file system or to a zip file
that can easily be shipped around.

## Create a Simple MLeap Pipeline

```scala
import ml.combust.bundle.BundleFile
import ml.combust.bundle.serializer.SerializationFormat
import ml.combust.mleap.core.feature.{OneHotEncoderModel, StringIndexerModel}
import ml.combust.mleap.core.regression.LinearRegressionModel
import ml.combust.mleap.runtime.transformer.Pipeline
import ml.combust.mleap.runtime.transformer.feature.{OneHotEncoder, StringIndexer, VectorAssembler}
import ml.combust.mleap.runtime.transformer.regression.LinearRegression
import org.apache.spark.ml.linalg.Vectors
import ml.combust.mleap.runtime.MleapSupport._
import resource._

// Create a sample pipeline that we will serialize
// And then deserialize using various formats
val stringIndexer = StringIndexer(inputCol = "a_string",
  outputCol = "a_string_index",
  model = StringIndexerModel(Seq("Hello, MLeap!", "Another row")))
val oneHotEncoder = OneHotEncoder(inputCol = "a_string_index",
  outputCol = "a_string_oh",
  model = OneHotEncoderModel(2, dropLast = false))
val featureAssembler = VectorAssembler(inputCols = Array(oneHotEncoder.outputCol, "a_double"),
  outputCol = "features")
val linearRegression = LinearRegression(featuresCol = featureAssembler.outputCol,
  predictionCol = "prediction",
  model = LinearRegressionModel(Vectors.dense(2.0, 3.0, 6.0), 23.5))
val pipeline = Pipeline(transformers = Seq(stringIndexer, oneHotEncoder, featureAssembler, linearRegression))
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

### Zip Bundle

```scala
// Deserialize a zip bundle
// Use Scala ARM to make sure resources are managed properly
val zipBundle = (for(bundle <- managed(BundleFile("jar:file:/tmp/mleap-examples/simple-mixed.zip"))) yield {
  bundle.loadBundle().get
}).opt.get
```

### Directory Bundle

```scala
// Deserialize a directory bundle
// Use Scala ARM to make sure resources are managed properly
val dirBundle = (for(bundle <- managed(BundleFile("file:/tmp/mleap-examples/simple-mixed-dir"))) yield {
  bundle.loadBundle().get
}).opt.get
```

