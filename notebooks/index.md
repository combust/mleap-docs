# Demo Notebooks

We have put together a number of notebooks to demonstrate MLeap usage with Spark, Scikit and soon TensorFlow. The demo notebooks can be found on our [mleap-demo](https://github.com/combust/mleap-demo) github repository. Child pages below also include instructions for how to include MLeap within your notebook environment of choice.

Our goal is to maintain parity between notebooks for Jupyter/Zeppelin/DataBricks and Spark/Scikit/TensorFlow. 
Here is a list of demo notebooks we have available today:

### AirBnb Price Prediction

Using a snapshot of historical AirBnb listings data, build a ML Pipeline to model the price of an appartment based on features like `number of rooms`, `number of bathrooms`, `square feet` and others. The notebook demonstrates how to train and serialize both a linear regression and a random forest model. This notebook also shows deserialization of a bundle as well as scoring of new LeapFrames without the Spark Context with MLeap Runtime.

* [Code for Spark](https://github.com/combust/mleap-demo/blob/master/notebooks/airbnb-price-regression.ipynb)
* [Code for PySpark](https://github.com/combust/mleap-demo/blob/master/notebooks/PySpark%20-%20AirBnb.ipynb)
* [Code for Scikit-Learn](https://github.com/combust/mleap-demo/blob/master/notebooks/airbnb-price-regression-scikit.ipynb)


### Lending Club Loan Approval

Using the public Lending Club dataset to determine whether the applicant is approved for a loan or not, we build an ML Pipeline and train a logistic regression and a random forest classifier. We also show an example of how to deploy the two models to an API server. For an example of deserialization and MLeap runtime, see the AirBnb demo above (we'll add it here too).

* [Code for Spark](https://github.com/combust/mleap-demo/blob/master/notebooks/lending-club-classifier-demo.ipynb)


### MNIST Digits Recognition

This demo uses the popular MNIST dataset and demonstrates usage and serialization of more complicated transformers like `PCA` to train a random forest classifier and serialize it to a bundle. The goal of this demo is to demonstrate that even for more complex transformers, MLeap saves developers potentially days of writing and maintaining code to support features like PCA and random forest models.

* [Code for Spark](https://github.com/combust/mleap-demo/blob/master/zeppelin/mnist-mleap-demo-code.json) - Zeppelin only for now