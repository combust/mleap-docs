# Leap Frame Operations

The basic operations of a leap frame are:

1. Creating new columns and inserting data
2. Dropping columns
3. Reading row data
4. Selecting a set of columns into a new leap frame

## Insert

Generating new values from existing fields is a simple task. Just
specify the name of the output field, a list of input fields, and a
Scala function to generate the new output value.

```scala
// Insert some values into the leap frame
val leapFrame2 = leapFrame.withOutput("generated_field", "a_string", "an_int") {
  (str: String, i: Int) => s"$str: $i"
}

// Extract our new data from the leap frame
val generatedStrings: Seq[String] = (for(lf <- leapFrame2;
    lf2 <- lf.select("generated_field", "a_string", "an_int")) yield {
  val str = lf2.dataset(0).getString(1) // get value of "a_string"
  val i = lf2.dataset(0).getInt(2) // get value of "an_int"
  assert(lf2.dataset(0).getString(0) == s"$str: $i")

  lf2.dataset.map(_.getString(0))
}).get.toSeq

// Print out our generated strings
//   > "Hello, MLeap!: 42"
//   > "Another row: 43"
println(generatedStrings.mkString("\n"))
```

### Insert Optional Value

Null values in MLeap are supported with the Scala `Option` monad. Let's
output some optionally null values in our leap frame.

```scala
// Insert some values into the leap frame
val leapFrame3 = leapFrame.withOutput("optional_double", "a_double", "an_int") {
  (d: Double, i: Int) =>
    if(i > 42) {
      Some(777)
    } else { None }
}

// Extract our new data from the leap frame
val optionalInts: Seq[Option[Int]] = (for(lf <- leapFrame3;
                                          lf2 <- lf.select("optional_int")) yield {
  lf2.dataset.map(_.optionInt(0))
}).get.toSeq

// Print out our optional ints
//   > Some(777)
//   > None
println(optionalInts.mkString("\n"))
```

## Drop

Drop a field from the leap frame.

```scala
assert(leapFrame.schema.hasField("a_double")
for(lf <- leapFrame.dropField("a_double")) {
  assert(!lf.schema.hasField("a_double"))
}
```

## Read

Gain access to the rows in the leap frame.

```scala
val data = leapFrame.dataset

assert(data.head == Row("Hello, MLeap!", 56.7d, 13.0f, 42, 67l))
assert(data(1) == Row("Another row", 23.4d, 11.0f, 43, 88l))

// Datasets are iterable over their rows
assert(data.toSeq.size == 2)
```

## Select

Construct a new leap frame by selecting fields.

```scala
assert(leapFrame.schema.hasField("a_double"))
assert(leapFrame.schema.hasField("a_string"))
assert(leapFrame.schema.hasField("an_int"))
assert(leapFrame.schema.fields.size == 5)

for(lf <- leapFrame.select("a_double", "a_string")) {
  assert(lf.schema.hasField("a_double"))
  assert(lf.schema.hasField("a_string"))
  assert(!lf.schema.hasField("an_int"))
  assert(lf.schema.fields.size == 2)
}
```

