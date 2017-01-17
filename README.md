# MLeap Documentation

MLeap is a common runtime and serialization format for entire machine learning pipelines. It supports Spark, Scikit-learn and Tensorflow for training pipelines and exporting them to an MLeap Bundle. The pipeline can then be deserialized into a Spark ML pipeline, Scikit-learn pipeline, Tensorflow graph, or MLeap pipeline for use as a scoring engine or general purpose transformer pipeline. MLeap itself offers a comprehensive engine for executing pipelines that is portable across the JVM.
