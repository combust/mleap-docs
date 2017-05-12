## Jupyter Setup

To run Spark within Jupyter we recommend using the Toree kernel.
We are going to assume you already have the following installed:
1. Python 2.x
2. PIP
3. Docker (required to install Toree)

### Install Jupyter

```bash
virtualenv venv

source ./venv/bin/activate

pip install jupyter
```

### Build and install Toree

Clone master into your working directory from Toree's [github repo](https://github.com/apache/incubator-toree/blob/master/README.md).

For this next step, you'll need to make sure that docker is running.

```bash
cd incubator-toree
make release
cd dist/toree-pip
pip install .

SPARK_HOME=<path to spark> jupyter toree install
```

### Launch Notebook with MLeap for Spark

The most error-proof way to add mleap to your project is to modify the kernel directly (or create a new one for Toree and Spark 2.0).

Kernel config files are typically located in `/usr/local/share/jupyter/kernels/apache_toree_scala/kernel.json`

Go ahead and add or modify `__TOREE_SPARK_OPTS__` like so:

```bash
"__TOREE_SPARK_OPTS__": "--packages com.databricks:spark-avro_2.11:3.0.1,ml.combust.mleap:mleap-spark_2.11:0.7.0,"
```

An alternative way is to use AddDeps Magics, but we've run into dependency collisions, so do so at your own risk:

`%AddDeps ml.combust.mleap mleap-spark_2.11 0.7.0 --transitive`

### Launch Notebook with MLeap for PySpark

First go through the steps above for launching a notebook with MLeap for Spark, then add the following to `PYTHONPATH`

```bash
    "PYTHONPATH": "/usr/local/spark-2.0.0-bin-hadoop2.7/python:/usr/local/spark-2.0.0-bin-hadoop2.7/python/lib/py4j-0.10.1-src.zip:/Users/mikhail/combust/combust-mleap/python",
```

### Launch Notebook with MLeap for Scikit-Learn

No need to modify the `kernel.json` directly, just instantiate the libraries like described [here](../scikit-learn/index.md).
