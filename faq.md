# MLeap Frequently Asked Questions

## Does MLeap Support Custom Transformers?

Absolutely - our goal is to make writing custom transformers easy. Writing a custom transformer is exactly the same as writing a transformer for Spark and MLeap. The only difference is that we pre-package all of the out-of-the-box Spark transformers with `mleap-runtime` and `mleap-spark`.

For documentation on writing custom transformers, see the [Custom Transformers](mleap-runtime/custom-transformer.md) page.

## What is MLeap Runtime's Inference Performance?

MLeap is optimized to deliver execution of ML Pipelines in microseconds (1/1000 of milliseconds, because we get asked to clarify this).

Actual executions speed will depend on how many nodes are in your pipeline, but we standardize benchmarking on our AirBnb pipeline and test it against using the `SparkContext` with a `LocalRelation` DataFrame.
The two sets of benchmarks share the same feature pipeline, comprised of vector assemblers, standard scalers, string indexers, one-hot-encoders, but at the end execute:

* Linear Regression: 6.2 microseconds (.0062 milliseconds) vs 106 milliseconds with Spark LocalRelation
* Random Forest: 6.8 microseconds (0.0068 milliseconds) vs 101 milliseconds with Spark LocalRelation

### MLeap Random Forest Transform Speed:

Random Forest: ~6.8 microseconds (68/10000)

| # of transforms | Total time (milliseconds) | Transform time (microseconds) |
|:---:|:---:|:---:|
| 1000 | 6.956204 | 7 |
| 2000 | 13.717578 | 7 |
| 3000 | 20.424697 | 7 |
| 4000 | 27.160537 | 7 |
| 5000 | 34.025757 | 7 |
| 6000 | 41.017156 | 7 |
| 7000 | 48.140102 | 7 |
| 8000 | 54.724859 | 7 |
| 9000 | 61.769202 | 7 |
| 10000 | 68.646654 | 7 |

### Run Our Benchmarks

To run our benchmarks, or to see how to test your own, see our [MLeap Benchmark](https://github.com/combust/mleap/tree/master/mleap-benchmark) project.

More benchmarks can be found on the [MLeap Benchmarks's README](https://github.com/combust/mleap/blob/master/mleap-benchmark/README.md).

## Why use MLeap Bundles and not PMML or Other Serialization Frameworks?

MLeap serialization is built with the following goals and requirements in mind:

1. It should be easy for developers to add `custom transformers` in Scala and Java (we are adding Python and C support as well)
2. Serialization format should be flexible and meet state-of-the-art performance requirements. MLeap serializes to protobuf 3, making scalable deployment and execution of large pipelines (thousands of features) and models like Random Forests and Neural Nets possible
3. Serialization should be optimized for ML Transformers and Pipelines
4. Serialization should be accessible for all environments and platforms, including low-level languages like C, C++ and Rust
5. Provide a common serialization framework for Spark, Scikit, and TensorFlow transformers (ex: a standard scaler executes the same on any framework)

## Is MLeap Ready for Production?

Yes, MLeap is used in production at a number of companies and industries ranging from AdTech, Automotive, Deep Learning (integrating Spark ML Pipelines with TF Inception model), and market research.

MLeap 0.5.0 release provides a stable serialization format and runtime API for ML Pipelines. Backwards compatibility will officially be guaranteed in version 1.0.0, but we do not foresee any major structural changes going forward.

## Why Not Use a SparkContext with a LocalRelation DataFrame to Transform?

APIs relying on Spark Context can be optimized to process queries in ~100ms, and that is often too slow for many enterprise needs. For example, marketing platforms
need sub-5 millisecond response times for many requests. MLeap offers execution of complex pipelines with sub-millisecond performance. MLeap's performance is attributed to supporting technologies like the Scala Breeze library for linear algebra.

## Is Spark MLlib Supported?

Spark ML Pipelines already support a lot of the same transformers and models that are part of MLlib. In addition, we offer a wrapper around MLlib SupportVectorMachine in our `mleap-spark-extension` module.
If you find that something is missing from Spark ML that is found in MLlib, please let us know or contribute your own wrapper to MLeap.

## How Does TensorFlow Integration Work?

Presently Tensorflow integration works by using the official Tensorflow
SWIG wrappers. We may eventually change this to use JavaCPP bindings, or
even take an erlang-inspired approach and have a separate Tensorflow
process for executing Tensorflow graphs. However we end up doing it, the
interface will stay the same and you will always be able to transform
your leap frames with the `TensorflowTransformer`.

## When Will Scikit-Learn Be Supported?

Scikit-Learn support is currently in beta and we are working to support the following functionality in the initial release in early March:
* Support for all scikit tranformers that have a corresponding Spark transformer
* Provide both serialization and de-serialization of MLeap Bundles
* Provide basic pandas support: Group-by aggregations, joins

## How Can I Contribute?

* Contribute an Estimator/Transformer from Spark or your own custom transformer
* Write documentation
* Write a tutorial/walkthrough for an interesting ML problem
* Use MLeap at your company and tell us what you think
* Talk with us on [Gitter](https://gitter.im/combust/mleap)

You can also reach out to us directly at `hollin@combust.ml` and `mikhail@combust.ml`

