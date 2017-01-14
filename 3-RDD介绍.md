## RDD是Spark的核心抽象  
`《Spark核心源码分析与开发实战》`
### 什么是RDD
---
弹性分布式数据集`(RDD,Resilient Distributed Datasets)`是Spark的核心抽象，是对分布式内存的抽象使用,它表示已被分区、只读的、并提供了一组丰富的操作方式来操作这些数据集合。这些数据集的全部或部分可以缓存在内存中,在多次计算中可以重复使用,省去了大量的磁盘IO操作。在这些操作中，诸如`map、flatMap、filter`等转换操作可以很好的契合了Scala的集合操作。除此之外,RDD还提供了诸如`join、groupBy、reduceByKey`等方便的操作,以支持常见的数据操作。

### 优势
通常来讲，针对数据处理有几种常见的模型,包括:`迭代计算(Iterative Algorithms)、交互式查询(Relational Queries)、批处理(MapReduce)、实时流处理(Stream Processing)`。
例如Hadoop MapReduce采用了`MapReduces`模型,Storm则采用了`(Stream Processing)`。
RDD混合了这四中模型,使得Spark可以应用于大多数大数据处理场景。


