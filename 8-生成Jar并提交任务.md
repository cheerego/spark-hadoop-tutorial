### 生成Jar包
1. 如第6节中，通过idea构建一个spark工程
2. 编写scala代码
```scala
// 简单版
object SparkPi{
    def main(args:Array[String]):Unit={
        println("hello spark,run in server")
    }
}


// 复杂版  操作spark
import org.apache.spark.{SparkConf, SparkContext}
import scala.math.random
/**
  * Created by placeless on 16/11/23.
  */
object SparkPi {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setMaster("spark://master:7077").setAppName("FirstSparkApp")
    val spark = new SparkContext(conf)
    val slices = if (args.length > 0) args(0).toInt else 2
    val n = slices * 100000
    val count = spark.parallelize(1 to n, slices).map { i =>
      val x = random() * 2 - 1
      val y = random() * 2 - 1
      if (x * x + y * y < 1) 1 else 0
    }.reduce(_ + _)
    println("Pi is roughly " + 4.0 * count / n)
    spark.stop()
  }
}

// 复杂版 官方示例版
// 这个例子在 /usr/local/spark-2.0.1/examples/src/main/scala/org/apache/spark/examples/SparkPi.scala
// 这个例子的jar在 /usr/local/spark-2.0.1/examples/jars/spark-examples-2.11-2.0.1.jar
import scala.math.random
import org.apache.spark.sql.SparkSession
object SparkPi{
    def main(args:Array[String]){
        val spark = SparkSession
            .builder
            .appName("Spark Pi")
            .getOrCreate()
        val slices = if (args.length > 0 ) args(0).toInt else 2
        val n = math.min(100000L * slices,Int.MaxValue).toInt
        val count =spark.sparkContext.parallelize(1 until n,slices).map{ i=>
            val x = random*2 - 1
            val y = random*2 - 1
            if(x*x +y*y < 1) 1 else 0
        }.reduce(_+_)
        printlin("Pi is roughlu "+4.0*count/(n-1)
        spark.stop()
    }
}
```
3.  构建jar
> - 进入Project Structur
> - 点击Artifacts -> + -> JAR -> From modules with dependencies
> - Main Class 填写 SparkPi
> - OK
> - 在菜单栏中找到Build -> Build Artifacts -> Build (如果是已经有了就Rebuild)
> - 生成的jar在 项目目录下的 /out/artifacts 下

4. 简单的修改  
将jar拷贝到桌面，右键->打开方式->WinRAR archiver,进入META-INF目录下，如果有*.RSA ，*.SF，*.DSA结尾的文件 都要删除，否则会抛出一个Java.lang.SecurityException。然后通sftp将这个jar包上传都master服务器

5. Run  
spark-submit --master spark://master:7077 --class SparkPi ~/hello-scala.jar
全都是没有看到异常一般是没有问题的，但是可能你看不到输出结果，那么就要仔细查看了，因为这条submit命令会输出很多东西，而且代码里面的print语句是在太短太不起眼，如果是在看不见，那么就改善一下print语句。
