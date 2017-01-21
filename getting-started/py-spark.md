# Getting Started with PySpark

MLeap PySpark integration provides serialization of PySpark-trained ML
pipelines to [MLeap Bundles](../mleap-bundle/). MLeap also provides
several extensions to Spark, including enhanced one hot encoding and one vs
rest models. Unlike Mleap<>Spark integration, MLeap doesn't' yet provide PySpark
integration with [Spark Extensions transformers.](../core-concepts/transformers/support.md#extensions)

## Adding MLeap Spark to Your Project

Before adding MLeap Pyspark to your project, you first have to compile and
add [MLeap Spark](./spark.md).

MLeap PySpark is available in the [combust/mleap](https://github.com/combust/mleap) github repository in the
python package.

To add MLeap to your PySpark project, just clone the git repo, add the `mleap/pyhton`
path, and import `mleap.pyspark`

```bash
git clone git@github.com:combust/mleap.git
```

Then in your python environment do:

```python
import sys
sys.path.append('<git directory>/mleap/python')

import mleap.pyspark 
```

Note: the import of `mleap.pyspark` needs to happen before any other PySpark
libraries are imported.

Note: If you are working from a notebook environment, be sure to take a look at
instructions of how to set up MLeap PySpark with:
* [Jupyter](../integration/jupyter-notebooks.md)
* [Zeppelin](../integration/zeppelin-notebooks.md)
* [Databricks](../integration/databricks-notebooks.md)

## Using PIP

PIP support for PySpark is coming soon.

To use MLeap extensions to PySpark:

1. See [build instructions](./building.html) to build MLeap from source.
2. See [core concepts](../core-concepts/) for an overview of ML pipelines.
3. See [Spark documentation](http://spark.apache.org/docs/latest/ml-guide.html) to learn how to train ML pipelines in Spark.
4. See [Demo notebook](https://github.com/combust/mleap-demo/blob/master/notebooks/PySpark%20-%20AirBnb.ipynb) on how to use PySpark and MLeap to serialize your pipeline to Bundle.ml
