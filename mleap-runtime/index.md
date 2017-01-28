# Basic Usage

MLeap Runtime is a lightweight, yet highly optimized, execution engine for Machine Learning Pipelines. The goal of MLeap Runtime is to provide production-level serving/scoring infrastructure for common machine learning frameworks, without the dependency on their core libraries. Meaning:
* Execute Spark ML Pipelines without the dependency on the spark context, distributed data frames, and costly execution plans
* Execute Scikit-learn pipelines without the dependency on numpy, pandas, scipy or other libraries used in training 

MLeap aims to be as simple as possible to use, and here are our design principles:

1. Use monadic programming as much as possible
2. Stay clean with automatic resource management
3. Report useful errors
4. Make operations on leap frames and transformers as natural and simple
   as possible

A lot of magic goes into making the API user-friendly, but you don't
have to worry about any of it unless you want to.

Let's start off with some basic usage of MLeap, like creating a leap
frame, modifying it, and finally using transformers and pipelines for
full-fledged ML pipeline transformations.

