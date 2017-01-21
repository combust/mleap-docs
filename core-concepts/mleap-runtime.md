# MLeap Runtime

The MLeap Runtime is a lightweight execution engine for machine learning
pipelines. It has the following features:

1. Data frames, called leap frames with support for all common data
   types and custom data types
2. Transformers, currently supports all transformers found in Spark as
   well as multiple extension transformers
3. Pipelines, easily build pipelines from your transformers
4. Full integration with MLeap Bundles, the MLeap runtime provides a
   reference implementation for MLeap Bundles and anyone who wishes to
implement their own serializers for it
5. Serialization formats for leap frames to easily send content across
   the wire
6. A very fast linear algebra system thanks to [Breeze](https://github.com/scalanlp/breeze) linear algebra

See our [basic usage](../basic) and [advanced usage](../advanced) for more
information on how to use the MLeap Runtime in your application.
