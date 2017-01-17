# MLeap Bundle Serialization

Serialization and deserialization is fully functional for all Spark and
MLeap transformers. These is partial support for Scikit-learn
serialization and Tensorflow serialization/deserialization is still in
planning. Once you have exported an MLeap Bundle, as long as all of the
operations in it are supported by your target platform, you can load it
into that platform for use. In its current state, this means that you
can serialize from Spark, PySpark, Scikit-learn or MLeap and deserialize
the same pipeline back into Spark, PySpark or MLeap. It's also worth
noting that Tensorflow graphs are supported by MLeap, so you can include
them in your ML pipelines.

## Serialization Formats

| Format | Description |
|---|---|
| JSON | Serialize all attributes and models as JSON |
| Protobuf | Serialize all attributes and models as protobuf objects |
| Mixed | Serialize small attributes and models as JSON, all large attributes and models will be serialized as Protobuf. |
