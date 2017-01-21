# Custom Transformers

Actually, every transformer in MLeap can be considered a custom
transformer. The only difference between the transformers and bundle
integration code you write and what we write is that ours gets included
in the release jars!

Because of this, there are plenty of examples in the [MLeap source code](https://github.com/combust/mleap)
for how to write your own transformers and make them serializable
to/from Spark and MLeap.

Let's go through a simple example of writing a custom transformer that
maps an input string to a different output string using a `Map[String, String]`
to store the data needed for transformation. We will call our custom
transformer: `StringMapper`.
