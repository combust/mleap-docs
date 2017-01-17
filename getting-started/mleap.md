# Getting Started with MLeap

The MLeap runtime itself provides everything needed to execute and
serialize entire ML pipelines. It does not include anything required for
training ML pipelines. In order to start with MLeap, you will need to
add it to your project.

## Adding MLeap to Your Project

MLeap and its snapshots are hosted on Maven Central and so should be
easily accessible via a maven build file or SBT. MLeap is currently
cross-compiled for Scala versions 2.10 and 2.11. We try to maintain
Scala compatibility with Spark.

### Using SBT

```sbt
libraryDependencies += "ml.combust.mleap" %% "mleap-runtime" % "0.5.0"
```

### Using Maven

```pom
<dependency>
  <groupId>ml.combust.mleap</groupId>
  <artifactId>mleap-runtime_2.11</artifactId>
  <version>0.5.0</version>
</dependency>
```

1. See [build instructions](./building.html) to build MLeap from source.
2. See [core concepts](../core-concepts/) for an overview of ML pipelines.
3. See [basic usage](../basic/) of MLeap to start transforming leap frames.

