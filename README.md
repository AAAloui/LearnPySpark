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