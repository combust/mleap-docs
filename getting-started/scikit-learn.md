# Getting Started with Scikit-Learn

MLeap Scikit-Learn integration provides serialization of Scikit-trained ML
pipelines to [MLeap Bundles](../mleap-bundle/). MLeap also provides
several extensions to Scikit, including [MLeap extensions transformers.](../core-concepts/transformers/support.md#extensions)

MLeap Scikit integration works by adding Bundle.ML serialization to Transformers, Pipelines and Feature Unions. 
It is important to note that because the core execution engine is in scala and is modeled after Spark transformers, 
that only supported transformers are those that are available in Spark and any libraries that extend Spark.
For a full list of supported scikit-transformers see the [supported transformers page](../core-concepts/transformers/support.md) or if you'd like support for custom transformers see the [custom transformers]() section.

## Adding MLeap Scikit to Your Project

To add MLeap to your Scikit project, just pip install MLeap

```bash
pip install mleap-python
```

Then in your python environment import MLeap extensions to any Scikit transformers you plan to serialize:

```python
# Extends Bundle.ML Serialization for Pipelines
import mleap.sklearn.pipeline

# Extends Bundle.ML Serialization for Feature Unions
import mleap.sklearn.feature_union

# Extends Bundle.ML Serialization for Base Transformers (i.e. LabelEncoder, Standard Scaler)
import mleap.sklearn.preprocessing.data

# Extends Bundle.ML Serialization for Linear Regression Models
import mleap.sklearn.base

# Extends Bundle.ML Serialization for Logistic Regression
import mleap.sklearn.logistic

# Extends Bundle.ML Serialization for Random Forest Regressor
from mleap.sklearn.ensemble import forest
```


For more information on how to use MLeap extensions to Scikit:

1. See [core concepts](../core-concepts/) for an overview of ML pipelines.
2. See [Scikit-learn documentation](http://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html) to learn how to train ML pipelines in Python.
3. See Scikit-learn documentation on how to use [Feature Unions](http://scikit-learn.org/stable/modules/generated/sklearn.pipeline.FeatureUnion.html) with pipelines
4. See [Demo notebook](https://github.com/combust/mleap-demo/) on how to use Scikit and MLeap to serialize your pipeline to Bundle.ml
5. [Learn](../basic/transofrm-leap-frame.md) how to transform a [DataFrame](../core-concepts/data-frames/index.md) using MLeap.
