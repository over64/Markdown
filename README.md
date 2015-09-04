##Screenly
On the server side:

	- Postgresql database for configuration storage
	- Rest API for configuration server backend written on Scala or Python
	- Single page application for admin written on AngularJS
On client side

	- some linux distro over raspberry
	- bare x-server for running browser in kiosk mode(HTML), video player and image view program(or browser?)
	- python bootstrapping script which will configure client on startup via REST API
	- some mechanism for reinitialization without reboot. For exmaple - tiny http server(on some port) which will wait command for reinitialization.
NAS

	- dedicated server for access content
	- http server like Nginx for static content (image, video)
	- Software for video streaming? I dont know now. It's difficult task to implement video streaming, but parallel downloading of large video files can cause large server load. But maybe not. Due to smart memory system of Linux kernel. If we need to synchronize video on many clients then we need this feature
## Scala & Python
I like Scala so much because it's great multiparadigmal language(FP + OOP) with concise syntax and type safety. Scala brings the best of OOP and FP, best of compiling and scripting languages. But Python is OK too for tasks where scripting language more usable. I'll provide code samples on Scala and Python.

## Task 1
```python
import re

def leftPad(str, pad):
	pad_str = ' ' * pad
	return re.sub('(?m)(.*)', pad_str + r'\1', str)
```
## Task 2
This task much more interesting))
```scala
import scala.util.Random

val rand = new Random
val array = (1 to 1000000).map(v => math.abs(rand.nextInt) % 10)
val idx = array.length - 1

//imperative
//const memory
//O(n^2) CPU
//correct if all numbers positive
//very slow on big collection, but if we don't have memory, we could use this approach
var max = 0
for (i <- 0 to idx; j <- 0 to idx; if i != j) {
	val next = array(i) * array(j)
	if(next > max)
		max = next
}


//functional
//const memory
//O(n^2) CPU
//The same as previous, but in functional way
//This solution is much more errorless because we haven't any mutations
val fres = (0 to idx).map { i =>
	(0 to idx).filter(_ != i).map { j =>
		array(i) * array(j)
	}.max
}.max

//via for comperhension
//for comprehension requires data materialization!
//O(n^2) memory
//O(n^2) CPU
//cause OOM on 100k collection. Worst solution
//be careful with for-comprehension in Scala xD
val forRes = (
for {
	i <- 0 to idx; 
	j <- 0 to idx;
	if i != j
} yield array(i) * array(j)
).max

//functional with sorting
//previous solutions takes too much CPU consumption
//this solution much more faster
//sorting requires data materialization
//O(n) memory
//O(nlog n) CPU
//fast, elegant and errorless
val sortRes = array.sorted.reverse.take(2).reduce(_ * _)
```
```python
# I used numpy for array sorting because standart Array.sort function will mutate source array
# which will cause WTF effect for calee
import random
import numpy

array = [random.randint(0,10) for i in xrange(1000000)]
sorted = numpy.sort(array)
res = sorted[-1] * sorted[-2]
```

N.B. In snippets, presented above I have not any checks for input data(e.g. source array size) but it must
exists in final solution. QuickSort is "default" sorting algoritm, but we must be very careful with sorting algorithm.
Quicksort is unstable for some datasets. If we know something about source array(e.g. degree of sorting) we should use 
best sorting algorithm for this dataset.

## Testing

About tests. It's very difficult question. Answer to this question depends on many factors.

	- Have we money/ time for this?
	- Is it need? Some software REQUIRES test. For example firmware for X-ray machine or program for control intercontinental ballistic rocket. In some software tests are optional.
	- What types of tests in question?
	 * Unit
	 * Integration
	 * Perfomance tests
	 * ... more and more

Answer to this question is specific for every case. I think we should use only this principal:
if we can organize continious delivery of qualitative product to user WITHOUT this kind of test then we should skip it in another case we MUST write these tests
