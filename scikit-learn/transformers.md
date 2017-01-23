# Scikit Transformers Examples

Here we outline some more complicated transformers and transformers that require additional processing to work nicely with Pipelines and Feature Unions.

### Label Encoder

The `LabelEncoder` is synonymous with `StringIndexer` in Spark, however there are a couple of unique features of the scikit transformer that we need to account for:
1. `LabelEncoder` only opperates on a single feature at a time
2. The output of the `LabelEncoder` is a numpy array of shape (1,n) instead of (n,1), which is required for further processing like One-Hot-Encoding

Here is what an example Pipeline looks like for a `LabelEncoder`

```python
# Create a dataframe with some a categorical and a continuous feature
df = pd.DataFrame(np.array([ ['Alice', 32], ['Jack', 18], ['Bob',34]]), columns=['name', 'age'])

# Define our feature extractor
feature_extractor_tf = FeatureExtractor(input_features=['name'], 
                                        output_vector='name_continuous_feature', 
                                        output_vector_items=['name_label_encoded'])

# Label Encoder for x1 Label 
label_encoder_tf = LabelEncoder()
label_encoder_tf.mlinit(input_features = feature_extractor_tf.output_vector, output_features='name_label_le')

# Reshape the output of the LabelEncoder to N-by-1 array
reshape_le_tf = ReshapeArrayToN1()

# Create our pipeline object and initialize MLeap Serialization
le_pipeline = Pipeline([(feature_extractor_tf.name, feature_extractor_tf),
                        (label_encoder_tf.name, label_encoder_tf),
                        (reshape_le_tf.name, reshape_le_tf)
                        ])
le_pipeline.mlinit()

# Transform our DataFrame
le_pipeline.fit_transform(df)

# output
array([[0],
       [2],
       [1]])
```

Next step is to combine the label indexer with a `OneHotEncoder`

### Scikit OneHotEncoder

We'll continue the example above to demonstrate how the out-of-the-box Scikit OneHotEncoder works.

```python
## Vector Assembler for x1 One Hot Encoder
one_hot_encoder_tf = OneHotEncoder(sparse=False) # Make sure to set sparse=False
one_hot_encoder_tf.mlinit(input_features = label_encoder_tf.output_features, output_features = '{}_one_hot_encoded'.format(label_encoder_tf.output_features))
#
##To Dense
to_dense_tf = ToDense(one_hot_encoder_tf.output_features)

# Construct our pipeline
one_hot_encoder_pipeline_x0 = Pipeline([
                                         (feature_extractor_tf.name, feature_extractor_tf),
                                         (label_encoder_tf.name, label_encoder_tf),
                                         (reshape_le_tf.name, reshape_le_tf),
                                         (one_hot_encoder_tf.name, one_hot_encoder_tf)
                                        ])

one_hot_encoder_pipeline_x0.minit()

# Execute our LabelEncoder + OneHotEncoder pipeline on our dataframe
one_hot_encoder_pipeline_x0.fit_transform(df)
matrix([[ 1.,  0.,  0.],
        [ 0.,  0.,  1.],
        [ 0.,  1.,  0.]])
```

One of the short-comings of Scikit's OneHotEncoder is that it's missing a `drop_last` functionality that's required in ML pipelines. 
MLeap comes with it's own OneHotEncoder that enables that function

### MLeap OneHotEncoder

Very similar to the Scikit OneHotEncoder, except we set an additional `drop_last` attribute.

```python
from mleap.sklearn.extensions.data import OneHotEncoder

## Vector Assembler for x1 One Hot Encoder
one_hot_encoder_tf = OneHotEncoder(sparse=False, drop_last=True) # Make sure to set sparse=False
one_hot_encoder_tf.mlinit(input_features = label_encoder_tf.output_features, output_features = '{}_one_hot_encoded'.format(label_encoder_tf.output_features))
#
##To Dense
to_dense_tf = ToDense(one_hot_encoder_tf.output_features)

# Construct our pipeline
one_hot_encoder_pipeline_x0 = Pipeline([
                                         (feature_extractor_tf.name, feature_extractor_tf),
                                         (label_encoder_tf.name, label_encoder_tf),
                                         (reshape_le_tf.name, reshape_le_tf),
                                         (one_hot_encoder_tf.name, one_hot_encoder_tf)
                                        ])

one_hot_encoder_pipeline_x0.minit()

# Execute our LabelEncoder + OneHotEncoder pipeline on our dataframe
one_hot_encoder_pipeline_x0.fit_transform(df)
matrix([[ 1.,  0.],
        [ 0.,  0.],
        [ 0.,  1.]])
```
