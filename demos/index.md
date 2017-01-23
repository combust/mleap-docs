# Basic Demo

This basic demo will guide you through using Spark to build and export
an ML pipeline to an MLeap Bundle and later use it to transform a data
frame using the MLeap Runtime.

## Build and Export an MLeap Bundle

In this section we will programmatically create a simple Spark ML
pipeline then export it to an MLeap Bundle. Our pipeline is very simple,
it performs string indexing on a categorical feature then runs the
result through a binarizer to force the result to a 1 or 0. This
pipeline has no real-world purpose, but illustrates how easy it is to
create MLeap Bundles from Spark ML pipelines.

```scala
import org.apache.spark.ml.feature.{StringIndexerModel, Binarizer}
import ml.combust.bundle.BundleFile
import ml.combust.mleap.spark.SparkSupport._
import resource._

// User out-of-the-box Spark transformers like you normally would
val stringIndexer = new StringIndexerModel(uid = "si", labels = Array("hello", "MLeap")).
  setInputCol("test_string").
  setOutputCol("test_index")

val binarizer = new Binarizer(uid = "bin").
  setThreshold(0.5).
  setInputCol("test_double").
  setOutputCol("test_bin")

// Use the MLeap utility method to directly create an org.apache.spark.ml.PipelineModel

import org.apache.spark.ml.mleap.SparkUtil

// Without needing to fit an org.apache.spark.ml.Pipeline
val pipeline = SparkUtil.createPipelineModel(uid = "pipeline", Array(stringIndexer, binarizer))

for(modelFile <- managed(BundleFile("jar:file:/tmp/simple-spark-pipeline.zip"))) {
  pipeline.writeBundle.
    // save our pipeline to a zip file
    // we can save a file to any supported java.nio.FileSystem
    save(modelFile)
}
```

## Import and MLeap Bundle

In this section we will load the MLeap Bundle from the first section
into the MLeap Runtime. We will then use the MLeap Runtime transformer to
transform a leap frame.

```scala
import ml.combust.bundle.BundleFile
import ml.combust.mleap.runtime.MleapSupport._
import resource._

// load the Spark pipeline we saved in the previous section
val bundle = (for(bundleFile <- managed(BundleFile("jar:file:/tmp/simple-spark-pipeline.zip"))) yield {
  bundleFile.loadBundle().get
}).opt.get

// create a simple LeapFrame to transform
import ml.combust.mleap.runtime.{Row, LeapFrame, LocalDataset}
import ml.combust.mleap.runtime.types._

// MLeap makes extensive use of monadic types like Try
val schema = StructType(StructField("test_string", StringType),
  StructField("test_double", DoubleType)).get
val data = LocalDataset(Row("hello", 0.6),
  Row("MLeap", 0.2))
val frame = LeapFrame(schema, data)

// transform the dataframe using our pipeline
val mleapPipeline = bundle.root
val frame2 = mleapPipeline.transform(frame).get
val data2 = frame2.dataset

// get data from the transformed rows and make some assertions
assert(data2(0).getDouble(2) == 0.0) // string indexer output
assert(data2(0).getDouble(3) == 1.0) // binarizer output

// the second row
assert(data2(1).getDouble(2) == 1.0)
assert(data2(1).getDouble(3) == 0.0)
```

That's it! This is a very simple example. Most likely you will not be
manually constructing Spark ML pipelines as we have done here, but
rather you will be using estimators and pipelines together to train on
your data and produce useful models. For a more advanced example, see
our [MNIST Demo](minst.md).

