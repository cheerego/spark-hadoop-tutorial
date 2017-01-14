### 文本操作以及DSL操作
通过下面案列展示Spark SQL操作文本的方式和DSL(Domain-specific language,领域特定语言)的具体使用。该例子在/usr/local/spark-2.0.1/examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala
### 数据准备
新建一个名为people.json的文本，里面填写内容如下：
```
{"name":"Michael"}
{"name":"Andy","age":30}
{"name":"Justin","age":19}
```
然后将people.json传入到HDFS的根目录
`hadoop fs -copyFromLocal / /path/to/people.json`
### 代码编写
```scala
import org.apache.spark.SparkConf
import org.apache.spark.sql.SparkSession

object SparkSQL{
    def main(args:Array[String]):Unit={
        val conf = new SparkConf().setMaster("spark://master:7077").setAppName("SparkSQLDemo")
        val spark = SparkSession
        .builder()
        .config(conf)
        .getOrCreate()
    //这个包用于将RDD隐式转换为DataFrame
    import spark.implicits._
    val df = spark.read.json("/people.json")
    //displays the content of the DataFrame to stdout
    df.show()
    }
}
```
### Spark-Submit 
详情请查看`生成Jar包并提交任务`
1. 生成Jar
2. 用解压缩软件打开Jar，删除META-INF中的三个特殊后缀的文件
3. SFTP上传到服务器
4. `spark-submit --master spark://192.168.1.6:7077 --class SparkSQL ~/sparksql.jar`