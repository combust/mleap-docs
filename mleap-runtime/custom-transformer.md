# Custom Transformers

Every transformer in MLeap can be considered a custom
transformer. The only difference between the transformers and bundle
integration code you write and what we write is that ours gets included
in the release jars. We welcome transformer additions to the MLeap project,
please make a PR.

There are plenty of examples in the [MLeap source code](https://github.com/combust/mleap)
for how to write your own transformers and make them serializable
to/from Spark and MLeap.

Let's go through a simple example of writing a custom transformer that
maps an input string to a double using a `Map[String, Double]`
to store the data needed for transformation. We will call our custom
transformer: `StringMap`. This is a transformer that is included in
MLeap source code, and you can view it here: [StringMapModel.scala](https://github.com/combust/mleap/blob/master/mleap-core/src/main/scala/ml/combust/mleap/core/feature/StringMapModel.scala).

## Overview

A brief overview of the steps involved:

1. Build our core model logic that can be shared between Spark and MLeap
2. Build the MLeap transformer
3. Build the Spark transformer
4. Build bundle serialization for MLeap
5. Build bundle serialization for Spark
6. Configure the MLeap Bundle registries with the MLeap and Spark
   custom transformer


## Core Model

The core model is the logic needed to transform the input data. It has no dependencies
on Spark or MLeap. In the case of our `StringMapModel`, it is a class that
knows how to map one string to a double. Let's see what this looks
like in Scala.

[StringMapModel.scala](https://github.com/combust/mleap/blob/master/mleap-core/src/main/scala/ml/combust/mleap/core/feature/StringMapModel.scala)
```scala
case class StringMapModel(labels: Map[String, Double]) extends Model {
  def apply(label: String): Double = labels(label)

  override def inputSchema: StructType = StructType("input" -> ScalarType.String).get

  override def outputSchema: StructType = StructType("output" -> ScalarType.Double).get
}
```

The case class has a set of labels that it know how to map to a double.
This is very similar to a `StringIndexerModel` except that the values of
the strings are arbitrary and not in sequence.

## MLeap Transformer

The MLeap transformer is the piece of code that knows how to execute
your core model against a leap frame. All MLeap transformers inherit
from a base class: `ml.combust.mleap.runtime.transformer.Transformer`.
For our example `StringMap` transformer, we can use a utility base class
for simple input/output transformers called:
`ml.combust.mleap.runtime.transformer.SimpleTransformer`. This base
class takes care of a small amount of boilerplate for any transformer
that has exactly one input and one output column.

Here is the Scala code for the MLeap transformer.

[StringMap.scala](https://github.com/combust/mleap/blob/master/mleap-runtime/src/main/scala/ml/combust/mleap/runtime/transformer/feature/StringMap.scala)
```scala
import ml.combust.mleap.core.feature.StringMapModel
import ml.combust.mleap.core.types.NodeShape
import ml.combust.mleap.runtime.function.UserDefinedFunction
import ml.combust.mleap.runtime.transformer.{SimpleTransformer, Transformer}

case class StringMap(override val uid: String = Transformer.uniqueName("string_map"),
                     override val shape: NodeShape,
                     override val model: StringMapModel) extends SimpleTransformer {
  override val exec: UserDefinedFunction = (label: String) => model(label)
```

Note the `UserDefinedFunction` exec. This is an MLeap
UserDefinedFunction that gets created from a Scala function using
reflection. UserDefinedFunctions are the primary way that MLeap allows
us to transform LeapFrames. The NodeShape shape defines the inputCol 
and outputCol for this transformer.

## Spark Transformer

The Spark transformer knows how to execute the core model against a
Spark DataFrame. All Spark transformers inherit from
`org.apache.spark.ml.Transformer`. If you have ever written a custom
Spark transformer before, this process will be very familiar.

Here is what a custom Spark transformer looks like in Scala.

[StringMap.scala](https://github.com/combust/mleap/tree/master/mleap-spark-extension/src/main/scala/org/apache/spark/ml/mleap/feature/StringMap.scala)
```scala
import ml.combust.mleap.core.feature.StringMapModel
import org.apache.spark.annotation.DeveloperApi
import org.apache.spark.ml.Transformer
import org.apache.spark.ml.param.ParamMap
import org.apache.spark.ml.param.shared.{HasInputCol, HasOutputCol}
import org.apache.spark.ml.util.Identifiable
import org.apache.spark.sql.functions._
import org.apache.spark.sql.{DataFrame, Dataset}
import org.apache.spark.sql.types._

class StringMap(override val uid: String,
                val model: StringMapModel) extends Transformer
  with HasInputCol
  with HasOutputCol {
  def this(model: StringMapModel) = this(uid = Identifiable.randomUID("string_map"), model = model)

  def setInputCol(value: String): this.type = set(inputCol, value)
  def setOutputCol(value: String): this.type = set(outputCol, value)

  @org.apache.spark.annotation.Since("2.0.0")
  override def transform(dataset: Dataset[_]): DataFrame = {
    val stringMapUdf = udf {
      (label: String) => model(label)
    }

    dataset.withColumn($(outputCol), stringMapUdf(dataset($(inputCol))))
  }

  override def copy(extra: ParamMap): Transformer = copyValues(new StringMap(uid, model), extra)

  @DeveloperApi
  override def transformSchema(schema: StructType): StructType = {
    require(schema($(inputCol)).dataType.isInstanceOf[StringType],
      s"Input column must be of type StringType but got ${schema($(inputCol)).dataType}")
    val inputFields = schema.fields
    require(!inputFields.exists(_.name == $(outputCol)),
      s"Output column ${$(outputCol)} already exists.")

    StructType(schema.fields :+ StructField($(outputCol), DoubleType))
  }
}
```

## MLeap Serialization

We need to define how to serialize/deserialize our model to/from an
MLeap Bundle. In order to do this, we make an implementation of
`ml.combust.mleap.bundle.ops.MleapOp` and `ml.combust.bundle.op.OpModel` for our
MLeap transformer and core model, respectively. These type classes are
all we need to define bundle serialization.

Here is what the serialization code looks like for our MLeap transformer
in Scala.

NOTE: The code below looks long, but most of it is auto-generated by the IDE.

[StringMapOp.scala](https://github.com/combust/mleap/blob/master/mleap-runtime/src/main/scala/ml/combust/mleap/bundle/ops/feature/StringMapOp.scala)
```scala
import ml.combust.bundle.BundleContext
import ml.combust.bundle.dsl._
import ml.combust.bundle.op.OpModel
import ml.combust.mleap.bundle.ops.MleapOp
import ml.combust.mleap.core.feature.StringMapModel
import ml.combust.mleap.runtime.MleapContext
import ml.combust.mleap.runtime.transformer.feature.StringMap

class StringMapOp extends MleapOp[StringMap, StringMapModel] {
  override val Model: OpModel[MleapContext, StringMapModel] = new OpModel[MleapContext, StringMapModel] {
    // the class of the model is needed for when we go to serialize JVM objects
    override val klazz: Class[StringMapModel] = classOf[StringMapModel]

    // a unique name for our op: "string_map"
    override def opName: String = Bundle.BuiltinOps.feature.string_map

    override def store(model: Model, obj: StringMapModel)
                      (implicit context: BundleContext[MleapContext]): Model = {
      // unzip our label map so we can store the label and the value
      // as two parallel arrays, we do this because MLeap Bundles do
      // not support storing data as a map
      val (labels, values) = obj.labels.toSeq.unzip

      // add the labels and values to the Bundle model that
      // will be serialized to our MLeap bundle
      model.withValue("labels", Value.stringList(labels)).
        withValue("values", Value.doubleList(values))
    }

    override def load(model: Model)
                     (implicit context: BundleContext[MleapContext]): StringMapModel = {
      // retrieve our list of labels
      val labels = model.value("labels").getStringList

      // retrieve our list of values
      val values = model.value("values").getDoubleList

      // reconstruct the model using the parallel labels and values
      StringMapModel(labels.zip(values).toMap)
    }
  }

  // the core model that is used by the transformer
  override def model(node: StringMap): StringMapModel = node.model
}
```

We will need to register `StringMapOp` with the MLeap bundle registry at
runtime to let MLeap know about it. We go over the registry later in
this article.

## Spark Serialization

We also need to define how to serialize/deserialize the custom Spark
transformer to/from MLeap. This is very similar to the process we took
for the MLeap transformer above. We will again be implementing both
`ml.combust.bundle.op.OpNode` and `ml.combust.bundle.op.OpModel`.

Here is what the serialization code looks like for StringMap in Scala.

[StringMapOp.scala](https://github.com/combust/mleap/tree/master/mleap-spark-extension/src/main/scala/org/apache/spark/ml/bundle/extension/ops/feature/StringMapOp.scala)
```scala
import ml.combust.bundle.BundleContext
import ml.combust.bundle.dsl._
import ml.combust.bundle.op.{OpModel, OpNode}
import ml.combust.mleap.core.feature.StringMapModel
import ml.combust.mleap.runtime.MleapContext
import org.apache.spark.ml.bundle.SparkBundleContext
import org.apache.spark.ml.mleap.feature.StringMap

class StringMapOp extends OpNode[SparkBundleContext, StringMap, StringMapModel] {
  override val Model: OpModel[SparkBundleContext, StringMapModel] = new OpModel[SparkBundleContext, StringMapModel] {
    // the class of the model is needed for when we go to serialize JVM objects
    override val klazz: Class[StringMapModel] = classOf[StringMapModel]

    // a unique name for our op: "string_map"
    // this should be the same as for the MLeap transformer serialization
    override def opName: String = Bundle.BuiltinOps.feature.string_map

    override def store(model: Model, obj: StringMapModel)
                      (implicit context: BundleContext[SparkBundleContext]): Model = {
      // unzip our label map so we can store the label and the value
      // as two parallel arrays, we do this because MLeap Bundles do
      // not support storing data as a map
      val (labels, values) = obj.labels.toSeq.unzip

      // add the labels and values to the Bundle model that
      // will be serialized to our MLeap bundle
      model.withValue("labels", Value.stringList(labels)).
        withValue("values", Value.doubleList(values))
    }

    override def load(model: Model)
                     (implicit context: BundleContext[SparkBundleContext]): StringMapModel = {
      // retrieve our list of labels
      val labels = model.value("labels").getStringList

      // retrieve our list of values
      val values = model.value("values").getDoubleList

      // reconstruct the model using the parallel labels and values
      StringMapModel(labels.zip(values).toMap)
    }
  }
  override val klazz: Class[StringMap] = classOf[StringMap]

  override def name(node: StringMap): String = node.uid

  override def model(node: StringMap): StringMapModel = node.model

  override def load(node: Node, model: StringMapModel)
                   (implicit context: BundleContext[SparkBundleContext]): StringMap = {
    new StringMap(uid = node.name, model = model).
      setInputCol(node.shape.standardInput.name).
      setOutputCol(node.shape.standardOutput.name)
  }

  override def shape(node: StringMap)(implicit context: BundleContext[SparkBundleContext]): NodeShape =
    NodeShape().withStandardIO(node.getInputCol, node.getOutputCol)
}
```

We will need to register this with the MLeap registry as well, so that
MLeap knows how to serialize this Spark transformer.

## MLeap Bundle Registries

A registry contains all of the custom transformers and types for a given
execution engine. In this case, we support the MLeap and Spark execution
engines for the `StringMap` transformer, so we will have to configure
both the Spark and MLeap registry to know how to serialize/deserialize
their respective transformers.

MLeap uses [Typesafe Config](https://github.com/typesafehub/config) to
configure registries. By default, MLeap ships with registries configured
for the Spark runtime and the MLeap runtime. You can take a look at each
of them here:

1. [MLeap Registry](https://github.com/combust/mleap/blob/master/mleap-runtime/src/main/resources/reference.conf)
2. [Spark Registry](https://github.com/combust/mleap/blob/master/mleap-spark/src/main/resources/reference.conf)

By default, the MLeap runtime uses the configuration at `ml.combust.mleap.registry.default`.
Spark uses the configuration at `ml.combust.mleap.spark.registry.default`.

### MLeap Registry

In order to add the custom transformer to the default MLeap registry, we will add a
`reference.conf` file to our own project that looks like this:

```conf
// make a list of all your custom transformers
// the list contains the fully-qualified class names of the
// OpNode implementations for your transformers
my.domain.mleap.ops = ["my.domain.mleap.ops.StringMapOp"]

// include the custom transformers we have defined to the default MLeap registry
ml.combust.mleap.registry.default.ops += "my.domain.mleap.ops"
```

### Spark Registry

In order to add the custom transformer to the default Spark registry, we will add a
`reference.conf` file to our own project that looks like this:

```conf
// make a list of all your custom transformers
// the list contains the fully-qualified class names of the
// OpNode implementations for your transformers
my.domain.mleap.spark.ops = ["my.domain.spark.ops.StringMapOp"]

// include the custom transformers ops we have defined to the default Spark registries
ml.combust.mleap.spark.registry.v20.ops += my.domain.mleap.spark.ops
ml.combust.mleap.spark.registry.v21.ops += my.domain.mleap.spark.ops
ml.combust.mleap.spark.registry.v22.ops += my.domain.mleap.spark.ops
```

