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
import ml.combust.bundle.BundleFile
import ml.combust.mleap.spark.SparkSupport._
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.bundle.SparkBundleContext
import org.apache.spark.ml.feature.{Binarizer, StringIndexer}
import org.apache.spark.sql._
import org.apache.spark.sql.functions._
import resource._

  val datasetName = "./mleap-docs/assets/spark-demo.csv"

  val dataframe: DataFrame = spark.sqlContext.read.format("csv")
    .option("header", true)
    .load(datasetName)
    .withColumn("test_double", col("test_double").cast("double"))

  // User out-of-the-box Spark transformers like you normally would
  val stringIndexer = new StringIndexer().
    setInputCol("test_string").
    setOutputCol("test_index")

  val binarizer = new Binarizer().
    setThreshold(0.5).
    setInputCol("test_double").
    setOutputCol("test_bin")

  val pipelineEstimator = new Pipeline()
    .setStages(Array(stringIndexer, binarizer))

  val pipeline = pipelineEstimator.fit(dataframe)

  // then serialize pipeline
  val sbc = SparkBundleContext().withDataset(pipeline.transform(dataframe))
  for(bf <- managed(BundleFile("jar:file:/tmp/simple-spark-pipeline.zip"))) {
    pipeline.writeBundle.save(bf)(sbc).get
  }
```

Dataset used for training can be found [here](../assets/spark-demo.csv).

NOTE: right click and "Save As...", Gitbook prevents directly clicking on the link.

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
  bundleFile.loadMleapBundle().get
}).opt.get

// create a simple LeapFrame to transform
import ml.combust.mleap.runtime.{Row, LeapFrame, LocalDataset}
import ml.combust.mleap.core.types._

// MLeap makes extensive use of monadic types like Try
val schema = StructType(StructField("test_string", ScalarType.String),
  StructField("test_double", ScalarType.Double)).get
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
our [MNIST Demo](../demos/minst.md).

