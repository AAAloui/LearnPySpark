# LearnPySpark
I will use this repo to track my work learning PySpark auditing edx  "Big Data Analytics Using Spark" Course. I will document steps I took and hopefully Help others who have similar learning objectives.

## Course Objectives
1. introduction to using large scale data analysis frameworks (Spark, XGBoost and TensorFlow).
2. combine methods from statistics and machine learning to perform large scale analysis, identify statistically significant pattern and visualize statistical summaries.

## Topics
*   Memory Hierarchy, latency vs. throughput.
*   Spark Basics
*   Dataframes and SQL
*   PCA and weather analysis
*   K-means and intrinsic dimensions
*   Decision trees, boosting, and random forests
*   Neural Networks and TensorFlow

## Memory Hierarchy
**Caching** combines both fast and slow memory to create storage that is both fast and large.
The cache is effective if the cache hit rate is high.</br>
**Temporal locality** is when a programm access the same address within a short time period.</br>
**Spacial locality** is when the program access elements that are close to each other in terms of their address.</br>
* Memory is partionned by block/line rather than bytes.
* Moving blocks of memory takes less time than moving each byte.
* Memory location that are close to each other are likely to fall in the same block.
which results in more cache hits.</br>

Sorting improves the locality of word counts.

Caching reduces latency by bringing relevant data closer to the CPU.
For caching to be effective we need to have either:
* Temporal locality.
* Spacial locality.

## Locality of Storage Access
* Access locality is the ability of software to make good use of the cache.
* Memory is broken into pages.
* Software that uses the same neighboringpages has good access locality.
* Hardware is designed to speed-up this kind of software.
* Keeping data in indexed structures optimizes memory locality because it uses the least amount of pages.

## Row-wise vs Column-wise Scanning
Scanning an array row-wise is more efficient than column-wise at memory locality. This effect increases proportionally to the number of elements of the array.

## SC: Spark Context
* PySpark program runs on the main node of a cluster, and control of other nodes is achieved through a special object called **Spark Context**
## RDD: Resilient Distributed Dataset
A mechanism for sharing memory between clusteer computers, abstracted from the programmer. ??
* Abstraction that describes the distributed data on the cluster macchines.
* A list whose elements are distributed over several computers.
* The main data structure in Spark.
* When in RDD form the list's elements can only be manipulated with RDD specific methods.
* RDDs are created from a list on the master node or from a file.
* RDDs can be translated back to lists using *collect* method.</br>
RDDs support two types of operations: **transformations**, which create a new dataset from an existing one, and **actions**, which return a value to the driver program after running a computation on the dataset.

## RDD Manipulation 
1. RDDs are **partitionned** accross worker nodes.
2. *RDD graph* defines the **lineage** of the RDDs.
3. SC devides the RDD into *stages* that define the execution plan (a stage is a set of operations that can be executed without materialization).
4. A **task** corresponds to one stage restricted to one partition.
5. An executor is a process that performs tasks.

## Operations on Plain RDDs
1. Create RDDs:
    * ```Parallelise()```
    * ...
2. Transformations RDD -> RDD:
    * ```map()```
3. Actions RDD -> Data on the Master Node:
    * ```collect()```
    * ```count()```
    * ```reduce()```
    
## Operations on Key-Value RDDs
Previous operations can be done on Key-Value RDDs.</br>
Special Key-Value RDDs operations:
1. Transformations RDD -> RDD:
    * ```map()``` and associate initial value as key (ex: ```map(lambda x: (x, x*x))```).
    * ```reduceByKey()``` reduces the elements after associating by the keys.
    * ```map()``` for getting itterator of values of keys (ex: ```myRdd.map(lambda k, iter : k,[x for x in iter]) ```)
2. Actions RDD -> Data on the Master Node:
    * ```countByKey()``` returns a python dictionnary of the count of each key.
    * ```lookup(key)``` returns a list of all values associated with a key.
    * ```collectAsMap()``` returns a dictionary of keys and lists of values.

## Lazy Evaluation
* Map operations don't **materialize** rdds, instead they describe a transformation graph that acts as an execution plan, and only materializes the rdd when we execute an action. This way spark saves memory and execution time.</br>
*  It is better to materialize rdds in some cases, we can do that using ```cache()``` method.</br>

## Partition and Gloming
* It is useful to use at least as many workers as partitions.
* In some cases it is better to use more partitions than workers, as it can help with load balancing.
* ```glom()``` allows workers to read rdds as tuples (immutable lists) in order to access specific locations on them (which defies the purpose of spark but can be useful sometimes)

## Spark Basics pt. 2
* **Chaining** is combining two or more operations in one command, it works the same way on spark (because it uses lazy evaluation) with the only difference being the intermediate RDDs is anonymous in this case.
* We can view the first element of the rdd using ```my_rdd.first()```
* We can view the first n elements of th RDD using ```my_rdd.take(n)```
* We can sample the content of an RDD using the method ```sample(b, p)``` (with b: with or without replacement as boolean value, p probability of picking a value (number of sample values we want/number of all values of the RDD))
* We can filter the values of an RDD using ```filter()``` (ex: ```my_rdd.filter(lambda x:x>0)```)
* We can filter out theduplicate values of an RDD using ```distinct()``` method. This operation requires a **shuffle** in order to detect duplicates across partitions.
* ```flatMap(func)``` is similar to ```map()```, the difference being that ```fltaMap``` maps every input element to 0 to many output elements, ex: 
``` 
text = ['hello world', 'this is a test']
text_rdd = sc.parallelize(text)
print("map = ",text_rdd.map(lambda line: line.split(' ')).collect())
print("flatMap = ",text_rdd.flatMap(lambda line: line.split(' ')).collect())

# output:
# map = [['hello','world'],['this','is','a','test']]
# flatMap = ['hello','world','this','is','a','test']
```
### Set Operations:
* **Union** of two RDDs: ``` my_rdd.union(my_other_rdd) ```
* **Intersection** of two RDDs:  ``` my_rdd.intersection(my_other_rdd) ```
* **Subtract** rdd of another rdd:   ``` my_rdd.subtract(my_other_rdd) ```
* **Cartesian** product of two RDDs:  ``` my_rdd.cartesian(my_other_rdd) ```

## Spark Dataframes
* Dataframes are efficient to store data tables in spark.
* All of the values in a column have the same type.
* The best way to save a dataframe in a file is a *parquet* file.
    * Parquet files allow us to access a subset of data directly, which is way more efficient than reading the whole data and filtering out the data we don't need.
