# MNIST Demo

This tutorial shows you how to use MLeap and Bundle.ML components to export a trained Spark ML Pipeline and use MLeap to transform new data without any dependencies on the Spark Context.

We will construct an ML Pipeline comprised of a Vector Assembler, a Binarizer, PCA and a Random Forest Model for handwritten image classification on the MNIST dataset. The goal of this exercise is not to train the optimal model, but rather to demonstrate the simplicity of going from training a pipeline in Spark and deploying that same pipeline (data processing + the algorithm) outside of Spark.

The code for this tutorial is split up into two parts:
* Spark ML Pipeline Code: Vanilla/out-of-the-box Spark code to train the ML Pipeline, which we serialize to Bundle.ML
* MLeap Code: Load the serialized Bundle to Mleap and transform Leap Frames

Some terms before we begin:

#### Nouns

* Estimator: The actual learning algorithms that train/fit the transformer against the data frame and produces a Model
* Model: In Spark, the model is the code and metadata needed to score against an already trained algorithm 
* Transformer: Anything that transforms a data frame, does not necessarily be trained by an estimator (i.e. a Binarizer)
* LeapFrame: A dataframe structure used for storing your data and the associated schema

### Train a Spark Pipeline

#### Load the data
```scala
// Note that we are taking advantage of com.databricks:spark-csv package to load the data
import org.apache.spark.ml.feature.{VectorAssembler,StringIndexer,IndexToString, Binarizer}
import org.apache.spark.ml.classification.{RandomForestClassificationModel, RandomForestClassifier}
import org.apache.spark.ml.evaluation.{MulticlassClassificationEvaluator}
import org.apache.spark.ml.{Pipeline,PipelineModel}
import org.apache.spark.ml.feature.PCA

val datasetPath = "./mleap-demo/data/mnist/mnist_train.csv"
var dataset = spark.sqlContext.read.format("com.databricks.spark.csv").
                 option("header", "true").
                 option("inferSchema", "true").
                 load(datasetPath)

val testDatasetPath = "./mleap-demo/data/mnist/mnist_test.csv"
var test = spark.sqlContext.read.format("com.databricks.spark.csv").
                 option("inferSchema", "true").
                 option("header", "true").
                 load(testDatasetPath)
```

You can download the [training](https://s3-us-west-2.amazonaws.com/mleap-demo/mnist/mnist_train.csv.gz) and [test](https://s3-us-west-2.amazonaws.com/mleap-demo/mnist/mnist_test.csv.gz) dataset (gzipped from s3) and of course you'll have to adjust the `datasetPath` and `testDatasetPath`.

The original data is hosted on [Yann LeCun's website](http://yann.lecun.com/exdb/mnist/).

#### Build the ML Data Pipeline

```scala
// Define Dependent and Independent Features
val predictionCol = "label"
val labels = Seq("0","1","2","3","4","5","6","7","8","9")
val pixelFeatures = (0 until 784).map(x => s"x$x").toArray

val layers = Array[Int](pixelFeatures.length, 784, 800, labels.length)

val vector_assembler = new VectorAssembler().
  setInputCols(featureColumns).
  setOutputCol("features")

val stringIndexer = new StringIndexer().
  setInputCol(predictionCol).
  setOutputCol("label_index").
  fit(dataset)


val binarizer = new Binarizer().
  setInputCol(vector_assembler.getOutputCol).
  setThreshold(127.5).
  setOutputCol("binarized_features")

val pca = new PCA().
  setInputCol(binarizer.getOutputCol).
  setOutputCol("pcaFeatures").
  setK(10)

val featurePipeline = new Pipeline().setStages(Array(vector_assembler, stringIndexer, binarizer, pca))

// Transform the raw data with the feature pipeline and persist it
val featureModel = featurePipeline.fit(dataset)

val datasetWithFeatures = featureModel.transform(dataset)

// Select only the data needed for training and persist it
val datasetPcaFeaturesOnly = datasetWithFeatures.select(stringIndexer.getOutputCol, pca.getOutputCol)
val datasetPcaFeaturesOnlyPersisted = datasetPcaFeaturesOnly.persist()
```

We could make the random forest model be part of the same pipeline, however, there is an existing bug ([SPARK-16845](https://issues.apache.org/jira/browse/SPARK-16845)] that prevents us from doing that (will be fixed in 2.2.0).

#### Train a Random Forest Model
```scala
// You can optionally experiment with CrossValidator and MulticlassClassificationEvaluator to determine optimal
// settings for the random forest

val rf = new RandomForestClassifier().
      setFeaturesCol(pca.getOutputCol).
      setLabelCol(stringIndexer.getOutputCol).
      setPredictionCol("prediction").
      setProbabilityCol("probability").
      setRawPredictionCol("raw_prediction")

val rfModel = rf.fit(datasetPcaFeaturesOnlyPersisted)
```

#### Serialize the ML Data Pipeline and RF Model to Bundle.ML
```scala
import org.apache.spark.ml.mleap.SparkUtil

val pipeline = SparkUtil.createPipelineModel(uid = "pipeline", Array(featureModel, rfModel))

val sbc = SparkBundleContext()
for(bf <- managed(BundleFile("jar:file:/tmp/mnist.model.rf.zip"))) {
        pipeline.writeBundle.save(bf)(sbc).get
      }
```

### Deserialize to MLeap and Score New Data

The goal of this step is to show how to deserialize a `bundle` and use it to score LeapFrames without any Spark dependencies. You can download the [mnist.json](https://s3-us-west-2.amazonaws.com/mleap-demo/mnist/mnist.json) from our s3 bucket.

```scala
import ml.combust.bundle.BundleFile
import ml.combust.mleap.MleapSupport._

// load the Spark pipeline we saved in the previous section
val bundle = BundleFile("/tmp/mnist-spark-pipeline.zip").load().get
```

Load the sample LeapFrame from the mleap-demo git repo (data/mnist.json)
```scala
import ml.combust.mleap.runtime.serialization.FrameReader

val s = scala.io.Source.fromURL("file:///./mleap-demo/mnist.json").mkString

val bytes = s.getBytes("UTF-8")
val frame = FrameReader("ml.combust.mleap.json").fromBytes(bytes)

// transform the dataframe using our pipeline
val mleapPipeline = bundle.root
val frame2 = mleapPipeline.transform(frame).get
val data = frame2.dataset
```

What next? You can find more examples and notebooks [here](https://github.com/combust/mleap-demo).
