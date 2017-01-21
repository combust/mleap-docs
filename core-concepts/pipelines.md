# Pipelines

Machine learning pipelines are series of transformers that execute on a
data frame. They allow us to combine our feature transformations
together with our actual predictive algorithms. Pipelines can be as
simple as a single transformer or quite complex, involving hundreds of
feature transformers and multiple predictive algorithms.


# Simple Pipeline

A simple pipeline could be taking a set of input columns that contain
doubles and combining them together into a feature vector which then
gets fed into a linear regression model.

```scala
import ml.combust.mleap.core.regression.LinearRegressionModel
import ml.combust.mleap.runtime.transformer.Pipeline
import ml.combust.mleap.runtime.transformer.feature.VectorAssembler
import ml.combust.mleap.runtime.transformer.regression.LinearRegression
import org.apache.spark.ml.linalg.Vectors

val assembler = VectorAssembler(uid = "simple_feature_assembler",
  inputCols = Array("feature1", "feature2", "feature3"),
  outputCol = "features")
val linearRegression = LinearRegression(uid = "simple_linear_regression",
  featuresCol = assembler.outputCol,
  predictionCol = "prediction",
  model = LinearRegressionModel(Vectors.dense(1.22, 3.5, 6.7), 3.4))
val pipeline = Pipeline(uid = "simple_pipeline", Seq(assembler, linearRegression))
```

# Advanced Pipelines

To see more advanced pipelines, please take a look at our [MLeap demo notebooks](https://github.com/combust/mleap-demo).

