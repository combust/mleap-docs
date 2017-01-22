# MLeap Scikit-Learn Integration

MLeap provides serialization functionality to Scikit Pipelines, Feature Unions and Transformers to Bundle.Ml in such a way that we maintain parity between Scikit and Spark transformers' functionality.
There are two main use-cases for MLeap-Scikit:
1. Serialize Scikit Pipelines and execute using MLeap Runtime
2. Serialize Scikit Pipelines and deseriazialize with Spark

As mentioned earlier, MLeap Runtime is a scala-only library today and we plan to add Python bindings in the future. However, it is enough to be able to execute pipelines and models without the dependency on Scikit, and Numpy.

## Extending Scikit with MLeap

There are a couple of important differences in how scikit transformers work and how Spark transformers work:
1. Spark transformers all come with `name`, `op`, `inputCol`, and `outputCol` attributes, scikit does not
2. Spark transformers can opperate on a vector, where as scikit operates on a n-dimensional arrays and matrices
3. Spark, because it is written in Scala, makes it easy to add implicit functions and attributes, with scikit it is a bit trickier and requires use of setattr()

Because of these additional complexities, there are a few paradigms we have to follow when extending scikit transformers with MLeap.
First is we have to initialize each transformer to include:
* Op: Unique `op` name - this is used as a link to Spark-based transformers (i.e. a Standard Scaler in scikit is the same as in Spark, so we have an op called `standard_scaler` to represent it)
* Name: A unique name for each transformer. For example, if you have multiple Standard Scaler objects, each needs to be assigned a unque name
* Input Column: Strictly for serialization, we set what the input column is
* Output Column: Strictly for serialization, we set what the output column is

### Scikit Transformer and Pipeline with MLeap

Let's first initialize all of the required libraries
```python
# Initialize MLeap libraries before Scikit/Pandas
import mleap.sklearn.preprocessing.data
import mleap.sklearn.pipeline
from mleap.sklearn.preprocessing.data import FeatureExtractor

# Import Scikit Transformer(s)
import pandas as pd
import numpy as np
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline
```

Then let's create a test DataFrame in Pandas

```python
# Create a pandas DataFrame
df = pd.DataFrame(np.random.randn(10, 5), columns=['a', 'b', 'c', 'd', 'e'])
```

Let's define two transformers, a feature extractor that will extract only the features we want to scale and Standard Scaler, which will perform the standard normal scaling operation.

```python
# Initialize a FeatureExtractor, which subselects only the features we want
# to run the Standard Scaler against
input_features = ['a', 'c', 'd']
output_vector_name = 'unscaled_continuous_features' # Used only for serialization purposes
output_features = ["{}_scaled".format(x) for x in input_features]

feature_extractor_tf = FeatureExtractor(input_features, 
                                        output_vector_name, 
                                        output_features)


# Define the Standard Scaler as we normally would
standard_scaler_tf = StandardScaler(with_mean=True,
                                    with_std=True)
                                 
# Execute ML-Init to add the require attributes to the transformer object
# Op and Name will be initialized automatically
standard_scaler_tf.mlinit(input_features=output_vector_name,
                          output_features=['sclaed_continuous_features'])
```
                       
Now that we have our transformers defined, we assemble them into a pipeline and execute it on our data frame

```python
# Now let's create a small pipeline using the Feature Extractor and the Standard Scaler
standard_scaler_pipeline = Pipeline([(feature_extractor_tf.name, feature_extractor_tf),
                                     (standard_scaler_tf.name, standard_scaler_tf)])
standard_scaler_pipeline.mlinit()

# Now let's run it on our test DataFrame
standard_scaler_pipeline.fit_transform(df)

# Printed output
array([[ 0.2070446 ,  0.30612846, -0.91620529],
       [ 0.81463009, -0.26668287,  1.95663995],
       [-0.94079041, -0.18882131, -0.0462197 ],
       [-0.43931405,  0.13214763, -0.10700743],
       [ 0.43992551, -0.2985418 , -0.89093752],
       [-0.15391539, -2.20828471,  0.5361159 ],
       [-1.07689244,  1.61019861,  1.42868885],
       [ 0.87874789,  1.43146482, -0.44362038],
       [-1.60105094, -0.40130005, -0.10754886],
       [ 1.87161513, -0.11630878, -1.40990552]])
```

## Combining Transformers

We just demonstrated how to apply a transformer to a set of features, but the output of that opperation is just a n-dimensional array that we would have to join back to our original data if we wanted to use it in say a regression model. Let's show how we can combine data from multiple transformers using Feature Unions.

First, go ahead and create another transformers, a MinMaxScaler on the remaining two features of the data frame:

```python
from sklearn.preprocessing import MinMaxScaler

input_features_min_max = ['b', 'e']
output_vector_name_min_max = 'unscaled_continuous_features_min_max' # Used only for serialization purposes
output_features_min_max = ["{}_min_maxscaled".format(x) for x in input_features_min_max]

feature_extractor_min_max_tf = FeatureExtractor(input_features_min_max, 
                                                output_vector_name_min_max, 
                                                output_features_min_max)


# Define the MinMaxScaler as we normally would
min_maxscaler_tf = MinMaxScaler()
                                 
# Execute ML-Init to add the require attributes to the transformer object
# Op and Name will be initialized automatically
min_maxscaler_tf.mlinit(input_features=output_vector_name_min_max,
                          output_features=['min_max_scaled_continuous_features'])
                          
# Assemble our MinMaxScaler Pipeline
min_max_scaler_pipeline = Pipeline([(feature_extractor_min_max_tf.name, feature_extractor_min_max_tf),
                                    (min_maxscaler_tf.name, min_maxscaler_tf)])
min_max_scaler_pipeline.mlinit()

# Now let's run it on our test DataFrame
min_max_scaler_pipeline.fit_transform(df)

array([[ 0.58433367,  0.72234095],
       [ 0.21145259,  0.72993807],
       [ 0.52661493,  0.59771784],
       [ 0.29403088,  0.19431993],
       [ 0.48838789,  1.        ],
       [ 1.        ,  0.46456522],
       [ 0.36402459,  0.43669119],
       [ 0.        ,  0.74182958],
       [ 0.60312285,  0.        ],
       [ 0.33707035,  0.39792128]])
```

Finaly, let's combine the two pipelines using a Feature Union. Note that you do not have to run the `fit`` or `fit_transform`` method on the pipeline before assembling the Feature Union.

```python
# Import MLeap extension to Feature Unions
import mleap.sklearn.feature_union

# Import Feature Union
from sklearn.pipeline import FeatureUnion

feature_union = FeatureUnion([
        (standard_scaler_pipeline.name, standard_scaler_pipeline),
        (min_max_scaler_pipeline.name, min_max_scaler_pipeline)
        ])
feature_union.mlinit()

# Create pipeline out of the Feature Union
feature_union_pipeline = Pipeline([(feature_union.name, feature_union)])
feature_union_pipeline.mlinit()

# Execute it on our data frame
feature_union_pipeline.fit_transform(df)

array([[ 0.2070446 ,  0.30612846, -0.91620529,  0.58433367,  0.72234095],
       [ 0.81463009, -0.26668287,  1.95663995,  0.21145259,  0.72993807],
       [-0.94079041, -0.18882131, -0.0462197 ,  0.52661493,  0.59771784],
       [-0.43931405,  0.13214763, -0.10700743,  0.29403088,  0.19431993],
       [ 0.43992551, -0.2985418 , -0.89093752,  0.48838789,  1.        ],
       [-0.15391539, -2.20828471,  0.5361159 ,  1.        ,  0.46456522],
       [-1.07689244,  1.61019861,  1.42868885,  0.36402459,  0.43669119],
       [ 0.87874789,  1.43146482, -0.44362038,  0.        ,  0.74182958],
       [-1.60105094, -0.40130005, -0.10754886,  0.60312285,  0.        ],
       [ 1.87161513, -0.11630878, -1.40990552,  0.33707035,  0.39792128]])
```

## Serialize to Zip File

In order to serialize to a zip file, make sure the URI begins with `jar:file` and ends with a `.zip`.

For example `jar:file:/tmp/mleap-bundle.zip`.

Note that you do have to fit your pipeline before serializing.

### JSON Format

Setting `init=True` tells the serializer that we are creating a bundle instead of just serializing the transformer.

```python
feature_union_pipeline.serialize_to_bundle('/tmp', 'jar:file:/tmp/mleap-bundle.zip', init=True)
```

### Protobuf Format

Coming Soon

### Mixed Format

Coming Soon

### Deserializing

Coming Soon