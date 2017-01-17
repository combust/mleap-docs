# Building MLeap From Source

MLeap is hosted on [Github](https://github.com/combust/mleap) as a
public project. Building MLeap is very straightforward and has very few
dependencies. Here are instructions for building from source.

## Install SBT

[Install Simple Build Tool](http://www.scala-sbt.org/0.13/docs/Setup.html),
which is used to build many Scala projects.

## Compiling Core MLeap

The core of MLeap includes every submodule except for Tensorflow
integration. We do not include Tensorflow in the core build because its
dependencies can be difficult to install.

### Clone the Github Repository

```bash
git clone https://github.com/combust/mleap.git
cd mleap
```

### Initialize Git Submodules

MLeap depends on a git submodule for all of the protobuf definitions, so
we need to initialize and update them.

```bash
git submodule init
git submodule update
```

### Compile MLeap

There are many submodules that make up the MLeap project. All of them
are aggregated to the root SBT project. This means running a command
from SBT will cause the command to be run on all subprojects.

```bash
sbt compile
```

### Run the Tests

MLeap has exensive testing, including full parity tests between MLeap
and Spark transformers.

```bash
sbt test
```

## Compiling Tensorflow Support

Compiling the `mleap-tensorflow` submodule does not happen
automatically. Instead, we first need to compile Tensorflow and install
the Tensorflow Java jar to our local maven2 repository.

### Compile/Install Tensorflow

Tensorflow has a great set of isntructions for compiling and installing.

1. [Tensorflow](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/g3doc/get_started/os_setup.md)
2. [Tensorflow Java Bindings](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/java).

### Build Tensorflow MLeap Module

```bash
sbt mleap-tensorflow/compile
```

### Run Tensorflow Integration Tests

```bash
TENSORFLOW_JNI=/path/to/tensorflow/library/folder/java sbt mleap-tensorflow/test
```
