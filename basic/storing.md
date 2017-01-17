# Storing a Leap Frame

We are able to save and load a leap frame using several different
serialization strategies. The provided formats are:

1. JSON
2. Avro
3. Binary

It is also possible to create your own serialization formats if these do
not suit your usage needs.

## Sample Leap Frame

All serialization examples will use this leap frame.

```scala
val schema = StructType(StructField("features", TensorType(DoubleType())),
  StructField("name", StringType()),
  StructField("list_data", ListType(StringType())),
  StructField("nullable_double", DoubleType(true)),
  StructField("float", FloatType(false)),
  StructField("byte_tensor", TensorType(ByteType(false))),
  StructField("short_list", ListType(ShortType(false))),
  StructField("nullable_string", StringType(true))).get
val dataset = LocalDataset(Row(Tensor.denseVector(Array(20.0, 10.0, 5.0)),
  "hello", Seq("hello", "there"),
  Option(56.7d), 32.4f,
  Tensor.denseVector(Array[Byte](1, 2, 3, 4)),
  Seq[Short](99, 12, 45),
  None))
val frame = LeapFrame(schema, dataset)
```

## JSON

```scala
// Store Leap Frame
for(bytes <- frame.writer("ml.combust.mleap.json").toBytes();
    frame2 <- FrameReader("ml.combust.mleap.json").fromBytes(bytes)) {
  println(new String(bytes)) // print the JSON bytes
  assert(frame == frame2)
}
```

## Avro

```scala
// Store Leap Frame
for(bytes <- frame.writer("ml.combust.mleap.avro").toBytes();
    frame2 <- FrameReader("ml.combust.mleap.avro").fromBytes(bytes)) {
  println(new String(bytes)) // print the Avro bytes
  assert(frame == frame2)
}
```

## Binary

Most efficient storage, uses data input/output streams to serialize the
leap frame data.

```scala
// Store Leap Frame
for(bytes <- frame.writer("ml.combust.mleap.binary").toBytes();
    frame2 <- FrameReader("ml.combust.mleap.binary").fromBytes(bytes)) {
  println(new String(bytes)) // print the binary bytes
  assert(frame == frame2)
}
```

## Custom

It is possible to create custom serializers for leap frames. Here is
how.
