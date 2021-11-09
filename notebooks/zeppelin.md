# Zeppelin MLeap Setup

Zeppelin runs Spark by default, and we won't be covering how to set-up a zeppelin notebook here. However, once you do have it set-up, adding MLeap as a dependency is very easy.

MLeap is already located on Maven Central, so all you have to do is add:
* `ml.combust.mleap:mleap-spark_2.12:0.18.1` for Spark serialization support
* `ml.combust.mleap:mleap-runtime_2.12:0.18.1` and `ml.combust.mleap:mleap-core_2.12:0.18.1` for MLeap Runtime support

Once that's done, just include mleap import statements:

```scala
// MLeap/Bundle.ML Serialization Libraries
import ml.combust.mleap.spark.SparkSupport._
import resource._
import ml.combust.bundle.BundleFile
import org.apache.spark.ml.bundle.SparkBundleContext
```
