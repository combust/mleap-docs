# Creating a Leap Frame

Let's create a leap frame to hold our data.

```scala
import ml.combust.mleap.runtime._
import ml.combust.mleap.runtime.types._

// Create a schema. Returned as a Try monad to ensure that there
// Are no duplicate field names
val schema: StructType = StructType(StructField("a_string", StringType()),
  StructField("a_double", DoubleType()),
  StructField("a_float", FloatType()),
  StructField("an_int", IntegerType()),
  StructField("a_long", LongType())).get

// Create a dataset to contain all of our values
// This dataset has two rows
val dataset: LocalDataset = LocalDataset(Row("Hello, MLeap!", 56.7d, 13.0f, 42, 67l),
  Row("Another row", 23.4d, 11.0f, 43, 88l))

// Create a LeapFrame from the schema and dataset
val leapFrame: DefaultLeapFrame = LeapFrame(schema, dataset)

// Make some assertions about the data in our leap frame
assert(leapFrame.dataset(0).getString(0) == "Hello, MLeap!")
assert(leapFrame.dataset(0).getDouble(1) == 56.7d)
assert(leapFrame.dataset(1).getDouble(1) == 23.4d)
```

Programatically creating leap frames like this can be very useful for
scoring data from, say, a web server or some other user input. It is
also useful to be able to load data from files and store data for later
use. See our [section on serializing leap frames](Serializing+Leap+Frames.html) for more
information.

