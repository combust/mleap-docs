# Boosting Transform Speed

MLeap's performance with transforming leap frames is good out of the
box, but sometimes there are situations where even more speed is needed.
Luckily, we have a row-based transformer that eliminates a lot of
overhead in transforming leap frames. The overhead that is being
eliminated is transforming the schema of the leap frame on every call to
transform. How is this done?

1. Calculate the input/output schema of a leap frame before any calls to
   transform
2. When we transform a row, preallocate enough space to store all
   required results
3. Fill the preallocated space with results from our transformers and
   return the transformed row

A lot of time is spent in transforming the schema relative to actually
processing your data, so this can be a huge speed increase in certain
cases.
