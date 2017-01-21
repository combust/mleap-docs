# Getting Started with Spark

MLeap Spark integration provides serialization of Spark-trained ML
pipelines to [MLeap Bundles](../mleap-bundle/). MLeap also provides
several extensions to Spark, including enhanced one hot encoding, one vs
rest models and unary/binary math transformations.

## Adding MLeap Spark to Your Project

MLeap Spark and its snapshots are hosted on Maven Central and so should be
easily accessible via a maven build file or SBT. MLeap is currently
cross-compiled for Scala versions 2.10 and 2.11. We try to maintain
Scala compatibility with Spark.

### Using SBT

```sbt
libraryDependencies += "ml.combust.mleap" %% "mleap-spark" % "0.5.0"
```

To use MLeap extensions to Spark:

```sbt
libraryDependencies += "ml.combust.mleap" %% "mleap-spark-extension" % "0.5.0"
```

### Using Maven

```pom
<dependency>
  <groupId>ml.combust.mleap</groupId>
  <artifactId>mleap-spark_2.11</artifactId>
  <version>0.5.0</version>
</dependency>
```

To use MLeap extensions to Spark:

```pom
<dependency>
  <groupId>ml.combust.mleap</groupId>
  <artifactId>mleap-spark-extension_2.11</artifactId>
  <version>0.5.0</version>
</dependency>
```

1. See [build instructions](./building.html) to build MLeap from source.
2. See [core concepts](../core-concepts/) for an overview of ML pipelines.
3. See [Spark documentation](http://spark.apache.org/docs/latest/ml-guide.html) to learn how to train ML pipelines in Spark.
4. See [Demo notebooks](https://github.com/combust/mleap-demo/tree/master/notebooks) on how to use MLeap with PySpark to serialize your pipelines to Bundle.ML and score with MLeap.