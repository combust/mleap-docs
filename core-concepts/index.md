# Core Concepts

MLeap uses several core building blocks to deployo your pipelines in an
easy-to-use manner.

| Concept | Description |
|---|---|
| Data Frames | Used to store data that is to be transformed, similar to a SQL table |
| Transformers | Take data from a data frame, apply some operation to it, and output new fields into the data frame |
| Pipelines | Use pipelines to execute a series of transformers against a data frame |
| Feature Unions (Scikit Only) | Use feature unions to execute Pipelines of transformers in parallel and join results at the end |
| MLeap Bundles | Used to store ML pipelines in a common JSON/Protobuf serialization format |
| MLeap Runtime | Used to execute an ML pipeline in the JVM using lightweight data structures |

This section is meant as an introduction to people who are unfamiliar
with the basics of machine learning pipelines and working with data
frames. Although, the sections on MLeap Bundles and the MLeap Runtime should be
useful to everyone.

