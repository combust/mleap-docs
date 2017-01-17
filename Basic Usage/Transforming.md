# Transforming a Leap Frame

Transformers are a useful abstraction for computing data in a data
frame, whether it is a leap frame in MLeap or a Spark data frame. Let's
see how to transform a data frame using a simple `StringIndexer`
transformer.

```scala
// Create a StringIndexer that knows how to index the two strings
// In our leap frame
val stringIndexer = StringIndexer(inputCol = "a_string",
  outputCol = "a_string_index",
  model = StringIndexerModel(Seq("Hello, MLeap!", "Another row")))

// Transform our leap frame using the StringIndexer transformer
val indices = (for(lf <- stringIndexer.transform(leapFrame);
                   lf2 <- lf.select("a_string_index")) yield {
  lf2.dataset.map(_.getDouble(0))
}).get.toSeq

// Make sure our indexer did its job
assert(indices == Seq(0.0, 1.0))
```

## Transforming a Leap Frame with a Pipeline

The above example isn't very interesting. The real power of data frames
and transformers comes when you build entire pipelines out of them,
going all the way from raw features to some sort of predictive
algorithm. Let's create a dummy pipeline that takes our indices from the
string indexer, runs them through a one hot encoder, then executes a
linear regression.

```scala
// Create our one hot encoder
val oneHotEncoder = OneHotEncoder(inputCol = "a_string_index",
  outputCol = "a_string_oh",
  model = OneHotEncoderModel(2, dropLast = false))

// Assemble some features together for use
// By our linear regression
val featureAssembler = VectorAssembler(inputCols = Array(oneHotEncoder.outputCol, "a_double"),
  outputCol = "features")

// Create our linear regression
// It has two coefficients, as the one hot encoder
// Outputs vectors of size 2
val linearRegression = LinearRegression(featuresCol = featureAssembler.outputCol,
  predictionCol = "prediction",
  model = LinearRegressionModel(Vectors.dense(2.0, 3.0, 6.0), 23.5))

// Create a pipeline from all of our transformers
val pipeline = Pipeline(transformers = Seq(stringIndexer, oneHotEncoder, featureAssembler, linearRegression))

// Transform our leap frame using the pipeline
val predictions = (for(lf <- pipeline.transform(leapFrame);
                       lf2 <- lf.select("prediction")) yield {
  lf2.dataset.map(_.getDouble(0))
}).get.toSeq

// Print our predictions
//   > 365.70000000000005
//   > 166.89999999999998
println(predictions.mkString("\n"))
```

This is the task that MLeap was meant for, executing machine learning
pipelines that were trained in Spark, PySpark, Scikit-learn or
Tensorflow.

