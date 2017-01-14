### 通过Spark提供的实例LocalPi测试
1. 启动Spark集群
2. 在Master节点上,进入Spark的bin目录下，用run-example命令运行Spark自带的实例LocalPi
```bash
cd /usr/local/spark/bin
./run-exmaple LocalPi spark://master:7070
Pi is roughly 3.14016  这个结果每次计算都可能不一样
```
```scala
object LocalPi {
  def main(args: Array[String]): Unit = {
    var count = 0
    for (i <- 1 to 100000) {
      val x: Double = math.random()*2-1
      val y: Double = math.random()*2-1
      if (x * x + y * y < 1) {
        count += 1
      }
    }
    println("Pi is roughly " + 4 * count / 100000.0)
  }
}
```

### 通过HDFS操作与Spark Shell测试
```bash
# 在HDFS中创建data目录
hadoop fs -mkdir /data 
# 查看data目录是否创建成功
hadoop fs -ls /
# 复制文件至HDFS的data目录下
hadoop fs -copyFromLocal /usr/local/spark/README.md /data/
# 复制操作没有报错，表示成功。 hadoop fs -ls /data 查看是否有README.md文件
# 在终端种启动spark shell
spark-shell
# 在启动spark shell后，会看到一个sc，这个sc指的是Spark Context，它是把代码提交到集群或者本地的通道。启动Spark shell时会自动生成sc
```
## 在Spark shell下编写代码，操作刚上传HDFS的"README.md"
```scala
scala> val rdd = sc.textFile("hdfs://master:9000/data/README.md")
scala> val count = rdd.filter(line=>line.contains("Spark")).count
上面两行代码的作用可以理解为，读取文件，然后在文件中查询“Spark”关键字出现的次数
```

